def G_BUILD_RECORD_PATH = 'buildRecord.json'

def G_EXTENSION_DLLS = [
    '\\\\torcaxfr1\\devtools\\ArkDbgX\\arkdbgx.dll',
    '\\\\torcaxfr1\\devtools\\ArkDbgX\\dxgkdx.dll',
    ]
    
def buildDebugAgentBundleImage() {
    echo "build debug agent image"
}

pipeline {
  agent any
  
  stages {
    stage('build') {
      steps {
          script {
            //Initialize build record
            def buildRecord = ['lastModifiedTime': [:]]
              
            for (String path in G_EXTENSION_DLLS) {
                buildRecord.lastModifiedTime[path] = 0
            }        
            
            //Load it with lastest data if possible
            if (fileExists(G_BUILD_RECORD_PATH)) {
                readJSON(file: G_BUILD_RECORD_PATH).lastModifiedTime.each {
                    file, time -> buildRecord.lastModifiedTime[file] = time
                }
            }
            
            //Check if any file is updated
            def extensionUpdated = false
            for (String path in G_EXTENSION_DLLS) {
                def file = new File(path)
                if (file.lastModified() > buildRecord.lastModifiedTime[path]) {
                    echo "Update ${path}, modified time ${new Date(file.lastModified()).format('EEE MMM dd hh:mm:ss a yyyy')}"
                    
                    buildRecord.lastModifiedTime[path] = file.lastModified()
                    extensionUpdated = true
                }
            }       
            
            if (extensionUpdated) {
                writeJSON(file: G_BUILD_RECORD_PATH, json: buildRecord)
                buildDebugAgentBundleImage()
            }
          }
      }
    }
  }
}
