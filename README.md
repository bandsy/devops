# DevOps

**Bandsy dev outline:**
> bandsy dev outline from a project manager and devops standpoint
- divided into important, start of dev, middle of dev, end of dev, and extra
- everything in a stage needs to be complete before moving on the next one
- theres also an information section at the bottom outlining important changes that everyone should be aware of

**Important:**
> anything that needs to be taken care of asap
- deadlines!
- seriously discuss promo/marketing stuff next meeting!
- ask about the design guy, the russian guy, and daniel
- get back to semi-consistent meetings (at least once a week)
- review our github org and repo setups, includes repo secrets, submodules repo?, etc.
- any working mongo deployment
- any working redis deployment
- centralised feinwaru credentials store (watch security)
- info wiki (github better than teams?)
- devops repo

**Start of dev:**
> stage where we are still starting up dev, most services arent up and running, were only making use of dev env (current stage)
- handling different dev envs for:
  - connectivity (internal and external)
  - databases
  - ci/cd
  - env vars
- handling env variables
- ts/cs dev automation scripts
- code style/linting, testing, and test coverage enforcement, git hooks
- kube namespace isolation
- monitor server costs, create budget including all other feinwaru expenses
- marketing, advertising, community (streaming?)
- bandsy branding incl. logo
- promo page

**Middle of dev:**
> stage where we have a basic version of each service up, starting to make use of preprod env, doing qa, etc.
- prod ready mongo
- prod ready postgres
- prod ready redis
- database migrations, backup, restore (pipelines and automation)
- monitoringno hostname but with subdomain will only crea
> stage where we have the major features complete, all thats required is testing, minor stuff, and bug fixes, everything needs to be prod ready by this point
- production ready checks for all systems
- kube deployments
- all user-facing services have compltete docs

**Extra:**
> research into tech and methodology that may not be of immediate use, will only do these if i happen to have time
- kube pod affinity
- kube cluster roles
- kube dns (in depth)
- databases (in depth - mongo, postgres, redis, elasticstack)
- database mirror db/env (*ref dev env databases)
- tooling:
  - infrastructure provisioning
  - configuration management
  - cloud design patterns
- mitigation, building for scale
- command line shit

**Information:**
> stuff i changed that probably affects you so please read this!
- the default branch on all repos except bandsy and devops is now develop
- branch naming conventions are as follows:
  - there are two main branches; master and develop
  - any other branch name should be formatted like so: branch/ticket_short-description
  - branch name should be all lowercase and can be one of the following:
    - hotfix
    - release
    - bug
    - feature
    - other branch names are acceptable but will be ignored in ci/cd and should not be merged to develop or master
  - ticket refers to the issue number on github
  - the description should be short and concise, with words seperated by hyphens
- branch protection rules will be set up as follows:
  - master:
    - includes admins
    - requires signed commits
    - requires github actions to pass
    - requires merge (cannot be pushed to)
    - merge requires 2+ reviews
    - should only be merged to from release/* and hotfix/*
  - develop:
    - includes admins
    - requires signed commits
    - requires github actions to pass
    - requires merge (cannot be pushed to)
    - merge requires 1+ reviews
    - should only be merged to from bug/* and feature/*
- kubernetes environments:
  - develop
  - preprod
  - production
- github actions will be set up as follows:
  > branches are not deployed automatically unless stated
  - master:
    - pr:
      - test
    - merge:
      - test
      - build (docker)
  - develop:
    - pr:
      - test
    - merge:
      - test
      - build (docker)
      - deploy (kube develop)
  - release/*:
    - push:
      - test
      - build (docker)
  - hotfix/*:
    - push:
      - test
  - bug/*:
    - push:
      - test
  - feature/*:
    - push:
      - test
- deployment will work as follows:
  - kube develop:
    - uses images built from the develop branch
    - auto-deployed through github actions
  - kube preprod:
    - uses images built from the release/* branches
    - needs to be manually deployed(`kubectl port-forward acid-postgres-main-0 <local-port>:5432`)
- databases: *(temporary)*
  > this section is not yet finalised and may receive major changes
  - postgres:
    - username: <service-name>-user
    - database: <service-name>-<kube-env>
    - pass: contact devops
    - cluster:
      - host: acid-postgres-main-0.default.cluster.local
      - port: 5432
    - local:
      > this requires port forwarding via kubectl (`kubectl port-forward acid-postgres-main-0 <local-port>:5432`)
      - host: localhost
      - port: check port forwarding command
    - ssl: require
  - mongo *(TBD)*
  - redis *(TBD)*
- connectivity: *(temporary)*
  > this section is not yet finalised and may receive major changes
  - internal:
    - <service-name>.<kube-env>.cluster.local
  - external:
    - only the web service should use these
    - all external connectivity is tls encoded
    - <kube-env>.<service-name>.bandsy.app
- development: *(temporary)*
  > this section is not yet finalised and may receive major changes
  - telepresence is recommended
  - if not using telepresence, port forward any required services using kubectl (`kubectl port-forward <service-name> <local-port>:<service-port>`)
  - environment variables should be used to differentiate between kube environments
- environment variables: *(temporary)*
  > this section is not yet finalised and may receive major changes
  - cluster:
    - configmaps should be used for non-sensitive data
    - secrets should be used for sensitive data (must be base64 encoded)
  - local:
    - .env files are recommended (remember to gitignore!)
    - dont include anything that would leak sensitive data in the docker container!
- kubectl: *(temporary)*
  - command line utility for interacting with the kube cluster
  - not required for development if using telepresence
  - can run on windows (linux-subsystem), macos, or linux
  - requires a config (contact devops)
- telepresence: *(temporary)*
  - command line utility designed to make microservice dev easier
  - forwards all trafic to/from service running on kube cluster to local machine
    - only need docker and telepresence for dev
    - can access all services running on the cluster using their internal connection strings
  - requires a config (contact devops)

