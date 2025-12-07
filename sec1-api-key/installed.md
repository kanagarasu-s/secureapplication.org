## Sec1 docs — for SBOM Scanner they show commands
```
curl --compressed https://digitalassets.sec1.io/sec1-sbom-scanner-macos -o sec1-sbom-scanner
chmod +x ./sec1-sbom-scanner
mv ./sec1-sbom-scanner /usr/local/bin/
```
## jenkins pipeline
```
stage('Sec1 Security Scan') {
            steps {
                script {
                    sec1ScaSastSecurity(
                        scanFileLocation: "${WORKSPACE}",
                        apiCredentialsId: 'Sec1-Api-Key',
                        applyThreshold: true,
                        actionOnThresholdBreached: 'unstable',
                        threshold: [
                            criticalThreshold: '0',
                            highThreshold: '0'
                        ]
                    )
                }
            }
        }
```
