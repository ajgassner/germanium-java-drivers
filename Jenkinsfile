properties([
    parameters([
        booleanParam(name: 'CLEAR_DOCKER_CACHE', defaultValue: false,
                description: 'Run without the docker cache.'),
        booleanParam(name: 'RUN_FIREFOX_TESTS', defaultValue: true,
                description: 'Shoult the firefox tests run'),
        booleanParam(name: 'RUN_CHROME_TESTS', defaultValue: true,
                description: 'Should the chrome tests run')
    ])
])

CLEAR_DOCKER_CACHE = Boolean.valueOf(CLEAR_DOCKER_CACHE)
RUN_FIREFOX_TESTS = Boolean.valueOf(RUN_FIREFOX_TESTS)
RUN_CHROME_TESTS = Boolean.valueOf(RUN_CHROME_TESTS)

def cucumberOptions=""

if (!RUN_FIREFOX_TESTS) {
    cucumberOptions += "-t ~@firefox "
}

if (!RUN_CHROME_TESTS) {
    cucumberOptions += "-t ~@chrome "
}

def mavenExtraParameters = ""
if (cucumberOptions != "") {
    mavenExtraParameters = "\"-Dcucumber.options=${cucumberOptions.trim()}\""
}

stage('Build Drivers') {
    node {
        deleteDir()

        checkout scm

        dockerRm containers: [
            'germanium_drivers_ok'
        ]

        dockerBuild file: './Dockerfile',
            no_cache: CLEAR_DOCKER_CACHE,
            tags: ['germanium_drivers:java']

        dockerRun image: 'germanium_drivers:java',
            name: 'germanium_drivers_ok',
            privileged: true,
            remove: false,
            env: [
                'DISPLAY=vnc-server:0',
                "MAVEN_EXTRA_PARAMETERS=${mavenExtraParameters}"
            ],
            networks: ['vnc'],
            volumes: [
                '/dev/shm:/dev/shm:rw'
            ]

        dockerCommit name: 'germanium_drivers_ok',
            image: 'germanium_drivers_ok:java'
    }
}

