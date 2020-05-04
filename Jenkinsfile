pipeline {
   agent any
   
   environment {
		KLOCWORK_URL = "http://localhost:8080"
		KLOCWORK_PROJECT = "kw_ci_pipeline_test"
		KLOCWORK_LICENSE_HOST = "localhost"
		KLOCWORK_LICENSE_PORT = "27000"
		KLOCWORK_LTOKEN = ""		
    }
   
   stages {
        stage('Configure project') {
            steps {
                bat label: '', script: '''cmake .'''
            }
        }
        stage('Klocwork Build') {
            steps {
                klocworkBuildSpecGeneration([additionalOpts: '', buildCommand: 'cmake --build .', ignoreErrors: false, output: 'kwinject.out', tool: 'kwinject'])
            }
        }
        stage('Klocwork Diff Analysis') {
            steps {
                klocworkIncremental([additionalOpts: '', buildSpec: 'kwinject.out', cleanupProject: true, differentialAnalysisConfig: [diffFileList: 'diff_file_list.txt', diffType: 'git', gitPreviousCommit: '${GIT_PREVIOUS_COMMIT}'], incrementalAnalysis: true, projectDir: '', reportFile: ''])
            }
        }
        stage('Quality gateway') {
            steps {
                klocworkFailureCondition([enableCiFailureCondition: true, failureConditionCiConfigs: [[enableHTMLReporting: true, enabledStatuses: [analyze: true, defer: true, filter: true, fix: true, fixInLaterRelease: true, fixInNextRelease: true, ignore: true, notAProblem: true], failUnstable: true, name: 'one or more', reportFile: '', threshold: '2']]])
            }
        }
    }  		
}