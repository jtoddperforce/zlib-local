pipeline {
   agent any
   
   stages {
        stage('Configure project') {
            steps {
                bat label: '', script: '''cmake .'''
            }
        }
        stage('Klocwork Build') {
            steps {
			    klocworkWrapper(installConfig: 'kwciagent path', ltoken: '', serverConfig: 'Local KW Server', serverProject: 'kw_ci_pipeline_test') {
                    klocworkBuildSpecGeneration([additionalOpts: '', buildCommand: 'cmake --build .', ignoreErrors: false, output: 'kwinject.out', tool: 'kwinject'])
				}
            }
        }
        stage('Klocwork Diff Analysis') {
            steps {
			    klocworkWrapper(installConfig: 'kwciagent path', ltoken: '', serverConfig: 'Local KW Server', serverProject: 'kw_ci_pipeline_test') {
                    klocworkIncremental([additionalOpts: '', buildSpec: 'kwinject.out', cleanupProject: true, differentialAnalysisConfig: [diffFileList: 'diff_file_list.txt', diffType: 'git', gitPreviousCommit: '${GIT_PREVIOUS_COMMIT}'], incrementalAnalysis: true, projectDir: '', reportFile: ''])
				}
            }
        }
        stage('Quality gateway') {
            steps {
			    klocworkWrapper(installConfig: 'kwciagent path', ltoken: '', serverConfig: 'Local KW Server', serverProject: 'kw_ci_pipeline_test') {
                    klocworkFailureCondition([enableCiFailureCondition: true, failureConditionCiConfigs: [[enableHTMLReporting: true, enabledStatuses: [analyze: true, defer: true, filter: true, fix: true, fixInLaterRelease: true, fixInNextRelease: true, ignore: true, notAProblem: true], failUnstable: true, name: 'one or more', reportFile: '', threshold: '2']]])
				}
            }
        }
    }  		
}