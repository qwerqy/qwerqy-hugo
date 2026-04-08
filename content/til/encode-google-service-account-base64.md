---
title: Encode Google Service Account to Base64
date: 2025-01-08
tags:
  - env
comment: Useful for long/json values
---

Example Google service account JSON:

```json
{
  "type": "service_account",
  "project_id": "test_project",
  "private_key_id": "abc123",
  "private_key": "-----BEGIN PRIVATE KEY-----\nblahblah\n-----END PRIVATE KEY-----\n",
  "client_email": "api@test_project.iam.gserviceaccount.com",
  "client_id": "107309273795607181234",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "https://www.googleapis.com/robot/v1/metadata/x509/api%40test_project.iam.gserviceaccount.com",
  "universe_domain": "googleapis.com"
}
```

Encode the file to base64. [Base64 Encoder](https://www.base64encode.org/)

Place the base64 string as an environment variable

```env
GSA_BASE64="ewogICJ0eXBlIjogInNlcnZpY2VfYWNjb3VudCIsCiAgInByb2plY3RfaWQiOiAidGVzdF9wcm9qZWN0IiwKICAicHJpdmF0ZV9rZXlfaWQiOiAiYWJjMTIzIiwKICAicHJpdmF0ZV9rZXkiOiAiLS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tXG5ibGFoYmxhaFxuLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLVxuIiwKICAiY2xpZW50X2VtYWlsIjogImFwaUB0ZXN0X3Byb2plY3QuaWFtLmdzZXJ2aWNlYWNjb3VudC5jb20iLAogICJjbGllbnRfaWQiOiAiMTA3MzA5MjczNzk1NjA3MTgxMjM0IiwKICAiYXV0aF91cmkiOiAiaHR0cHM6Ly9hY2NvdW50cy5nb29nbGUuY29tL28vb2F1dGgyL2F1dGgiLAogICJ0b2tlbl91cmkiOiAiaHR0cHM6Ly9vYXV0aDIuZ29vZ2xlYXBpcy5jb20vdG9rZW4iLAogICJhdXRoX3Byb3ZpZGVyX3g1MDlfY2VydF91cmwiOiAiaHR0cHM6Ly93d3cuZ29vZ2xlYXBpcy5jb20vb2F1dGgyL3YxL2NlcnRzIiwKICAiY2xpZW50X3g1MDlfY2VydF91cmwiOiAiaHR0cHM6Ly93d3cuZ29vZ2xlYXBpcy5jb20vcm9ib3QvdjEvbWV0YWRhdGEveDUwOS9hcGklNDB0ZXN0X3Byb2plY3QuaWFtLmdzZXJ2aWNlYWNjb3VudC5jb20iLAogICJ1bml2ZXJzZV9kb21haW4iOiAiZ29vZ2xlYXBpcy5jb20iCn0="
```

Decode and parse the JSON when asked for credential

```javascript
const credentials = JSON.parse(
  Buffer.from(process.env.GSA_BASE64, "base64").toString("utf-8")
)

// Google Sheets API setup
const auth = new google.auth.GoogleAuth({
  credentials,
  scopes: ["https://www.googleapis.com/auth/spreadsheets"]
})
```
