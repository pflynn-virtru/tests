# tests
Tests for OpenTDF

## [Vulnerability](vulnerability)

Automated checks for vulnerabilities identified during penetration testing

Place to run frontend and backend together locally.
Check Backend "Quick Start and Development" for [Prerequisites](https://github.com/opentdf/backend#prerequisites)

1) delete `ctlptl delete cluster kind-kind` and clear saved related images in docker if you've run integration tests locally from other folder
2) run `ctlptl create cluster kind --registry=ctlptl-registry`
3) `cd vulnerability`
4) `tilt up`

If you are running locally on mac frontend 'npm run build' step may take too long. Possible solution is run this
command `npm run build` and change frontend/Dockerfile line `RUN npm run build` to `COPY build/ build/` so it won`t
run it inside docker. Be careful not to push this changes, we won't need that to CI machines that runs on linux.

## [Abacus and API integration tests](abacus-and-api-integration-tests)

Automated Playwright tests for Abacus application run against the latest versions of frontend and backend together + API tests for OpenTDF backend services

Check Backend "Quick Start and Development" for [Prerequisites](https://github.com/opentdf/backend#prerequisites)

1) delete `ctlptl delete cluster kind-kind` and clear saved related images in docker if you've run integration tests locally from other folder
2) run `ctlptl create cluster kind --registry=ctlptl-registry`
3) `cd abacus-and-api-integration-tests`
4) `tilt up`

## Cross-client compatibility tests (xtests)
1) `cd xtest`
2) `npm ci && npm i @opentdf/client@CLIENT_VERSION`
3) `pip3 install -r ./requirements.txt`
4) `tilt up`

### To use a Github Package Manager Version

Before doing theabove, [configure Github packages as the scope provider for opentdf](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-npm-registry#authenticating-to-github-packages)

```
npm login --scope=@opentdf --registry=https://npm.pkg.github.com
```

### Run workflow locally using Act

https://github.com/nektos/act

#### Prerequisites 

```shell
brew install act
```

#### Secrets

- Create `secrets.env`
- Create new personal access token https://github.com/settings/tokens/new
- Copy token and paste in `secrets.env`
- Configure SSO for new token https://github.com/settings/tokens

`secrets.env`
```dotenv
GITHUB_TOKEN=<ghp_...>
```

#### Colima

Need about 80 GB space

```shell
colima start --disk 80
```

#### Prepare to run

Image takes time to download (Note update tag if updated by `act`)

```shell
export DOCKER_HOST=$(docker context inspect --format '{{.Endpoints.docker.Host}}')
docker pull image=catthehacker/ubuntu:full-20.04
```

#### Run

```shell
export DOCKER_HOST=$(docker context inspect --format '{{.Endpoints.docker.Host}}')
act --action-offline-mode --container-architecture linux/amd64 --secret-file secrets.env --container-options "--group-add $(stat -c %g /var/run/docker.sock)"

act --action-offline-mode --container-architecture linux/amd64 --secret-file secrets.env --privileged
```

To run individual workflow, see top of some yaml in `.github/workflows` 
