# Alpine(3.18.4) image with openssh installed and set as entrypoint

The working directory is `/workdir`.


## Examples GitLab (.gitlab-ci.yml)

```yaml
image: cowrvalera/docker-openssh-client:1.0.0


#SETUP:

# SSH_PRIVATE_KEY - your secret(as file + newline at the end)
# DEPLOY_DOMAIN - your domain or ip
# DEPLOY_DIR - <project-dir-full-path>

stages:
  - deploy

deploy:
  stage: deploy
  only:
    - main
  before_script:
    - eval $(ssh-agent -s)
    - cat "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" >> ~/.ssh/config'
  script:
  
    - ssh deployuser@$DEPLOY_DOMAIN "cd $DEPLOY_DIR && docker-compose down"
    - ssh deployuser@$DEPLOY_DOMAIN "cd $DEPLOY_DIR && git pull"
    - ssh deployuser@$DEPLOY_DOMAIN "cd $DEPLOY_DIR && docker-compose up -d"

```

## Examples Generate Keys(ed25519)
```sh
$ docker run --rm -it -v ".:/workdir/" cowrvalera/docker-openssh-client:1.0.0  ssh-keygen -t ed25519 -f /workdir/ssh_host_ed25519_key
```

## License
MIT - see LICENSE