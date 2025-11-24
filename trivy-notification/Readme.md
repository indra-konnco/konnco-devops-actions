# Trivy Notification Composite Action

This composite action performs:
- 🔍 Trivy vulnerability scanning
- 📄 Uploads the scan report as a GitHub artifact
- 📢 Sends a Telegram notification (supports Topic/Thread)
- 🔐 Logs into Huawei SWR to pull protected images

**This pipeline is designed to be called for automated Trivy security scanning.**

## 🧩 Inputs
| Name | Required | Description | 
|------|--------- |-------------|
| image	| ✔	| Full Docker image reference (example: swr.ap-southeast-4.myhuaweicloud.com/ns/app:tag) |
| image_name | ✔	| Application name (used for report filename) |
| region	| ✔	| Huawei SWR region (example: ap-southeast-4) |

## 🔐 Required Secrets (via env:)
| Environment Var | Description |
|-----------------|-------------|
| HUAWEI_SWR_USERNAME	| Username for SWR |
| HUAWEI_SWR_PASSWORD	| Password for SWR |
| TELEGRAM_BOT_TOKEN_SECURITY |	Telegram bot token |
| TELEGRAM_CHAT_ID_SECURITY	| Chat ID or group ID | 
| TELEGRAM_TOPIC_ID_SECURITY | Topic/Thread ID (optional but recommended) |

## 📘 Usage Example

Add this to `.github/workflows/pipeline.yml` :

```yaml
security-scan:
  name: Security Scanning Trivy
  needs: build-and-push
  runs-on: self-hosted

  steps:
    - name: Trivy Notification
      uses: indra-konnco/konnco-devops-actions/trivy-notification@test
      with:
        image: "(SWR_REGION_NAME).myhuaweicloud.com/konnco-spn/(APP_NAME):${{ needs.build-and-push.outputs.TAG }}"
        image_name: "(APP_NAME)"
        region: "(REGION)"
      env:
        TELEGRAM_BOT_TOKEN_SECURITY: ${{ secrets.TELEGRAM_BOT_TOKEN_SECURITY }}
        TELEGRAM_CHAT_ID_SECURITY: ${{ secrets.TELEGRAM_CHAT_ID_SECURITY }}
        TELEGRAM_TOPIC_ID_SECURITY: ${{ secrets.TELEGRAM_TOPIC_ID_SECURITY }}
        HUAWEI_SWR_PASSWORD: ${{ secrets.HUAWEI_SWR_PASSWORD }}
        HUAWEI_SWR_USERNAME: ${{ secrets.HUAWEI_SWR_USERNAME }}
```

## 📤 Artifact Output

The action generates a report file:

`trivy-report-<image_name>-YYYY-MM-DD-HH-MM.txt`


And uploads it as GitHub artifact:

```
trivy-report-<image_name>

📢 Telegram Message Format (Example)
🔐 Trivy Scan Completed - (APP_NAME)

👤 Pushed by: (NAME)
🖼️ Image: (SWR_REGION_NAME).myhuaweicloud.com/(SWR_ORGANIZATION)/(APP_NAME):latest

Severity Summary:
🔥 CRITICAL: 2
⚠️ HIGH: 5
⚡ MEDIUM: 12
📘 LOW: 8

📄 Artifact: <GitHub Actions URL>
```
