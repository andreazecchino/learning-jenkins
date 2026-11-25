# Set Credentials for Docker Registry

Create the `htpasswd` file with encrypted credentials:

```bash
mkdir -p auth
docker run --rm --entrypoint htpasswd httpd:2.4-alpine -Bbn [username] [password] > auth/htpasswd
```

Update the `registry.local` service with new environment variables and mount `auth/` folder as volume:

```yaml
registry:
  # ...
  environment:
    REGISTRY_AUTH: htpasswd
    REGISTRY_AUTH_HTPASSWD_REALM: Registry
    REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
  volumes:
    # ...
    - ./auth:/auth:ro
```

In Jenkins:

- *Manage Jenkins → Credentials → Add Credentials*
- Select *Username with password* type
- Set ID as `credentials-id`.

Update Stage `Push Docker Image`:

```groovy
stage('Push Docker Image') {
    when { expression { params.PUSH } }
    steps {
        script {
            docker.withRegistry("https://${REGISTRY}", 'credentials-id') {
                def img = docker.image("${REF}:${TAG}")
                img.push("${env.BUILD_ID}")
                img.push("${TAG}")
                img.push('latest')
            }
        }
    }
}
```

