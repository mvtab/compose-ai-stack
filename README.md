# compose-ai-stack
This is a simple stack of a graphical AI flow manager (flowise), a framework for 
AI inference (localai), cache management (redit), static content management and 
possibly vector store capabilities (postgres - pgvector), searching and indexing 
management (opensearch) and a reverse proxy (traefik).  
All containarized and stuffed together in a docker compose file. 

## Motivation
Really just trying out AI.

## Requirements

#### Software
- docker
- docker-compose
- nvidia-container-toolkit
- nvidia drivers (tested on Arch proprietary package 'nvidia' 565.77-9)

## Installation

A simple demo installation can be achieved with:
```bash
git clone https://github.com/mvtab/compose-ai-stack
cd compose-ai-stack
docker compose up -d

# At first run, give it some time and check localai logs, until models are downloaded.
# Traefik always needs around 30 seconds to fully startup the services. 

# When all services healthy:
curl http://flowise.localhost
curl http://localai.localhost
# Or visit them in browser.

```

## Usage
Flowise and LocalAI can be found at http://flowise.localhost and 
http://localai.localhost, respectively.  

#### Service names
When using services like Redis in Flowise, you can address all services by their 
docker service names, i.e. for LocalAI (base URL): http://localai:8080/v1.

## Configuration
All configurations can be done directly in the files from the .env_files directory.  
The structure is made so for easy sharing of passwords between containers, aswell as
easily readable (and therefor manageable) configurations.

It should be easy to add any other containers and use them from flowise without 
actually exposing them to the host. The latter is then achieveable by adding two
labels for Traefik. 

#### PostgreSQL
The PostgreSQL container uses a version with pgvector enabled. (pgvector/pgvector)  

###### Configuration files
- `.env_files/.postgres.yaml`: General configurations,
- `.env_files/.postgres.secrets.yaml`: `POSTGRES_PASSWORD`, aswell as an alias `DATABASE_PASSWORD` for Flowise (Secret gets mounted into the Flowise container aswell),
- `.env_files/.postgres.init.sql`: `vector` extension initialization. 

###### Defaults
Default username/password: `aistack/blablatester1`  
Default db: `aistack`  

#### Redis
Simple redis installation, kept minimal.  

###### Configuration files
- `.env_files/.redis.yaml`: General configurations,
- `.env_files/.redis.secrets.yaml`: Redis password.

###### Defaults
Default username/password: `default/blablatester2`

#### OpenSearch
Simple OpenSearch installation, kept minimal. Authentication still missing.  

###### Configuration files
- `.env_files/.opensearch.yaml`: General configurations.

#### LocalAI
LocalAI container optimized for Nvidia graphics cards.  

###### Configuration files
- `.env_files/.localai.yaml`: General configurations,
- `.env_files/.localai.secrets.yaml`: LocalAI API key.

###### Models
In order to preload different models, change the `LOCALAI_MODELS` variable in `.env_files/.localai.yaml`.

###### Other graphics card
You can use LocalAI with other graphics cards. Check [this](https://localai.io/basics/container/) section for more information.
Long story short, you have to: 
- Change container image,
- Change docker compose `deploy` top-level element configuration,
- Change `BUILD_TYPE` in `.env_files/.localai.yaml` appropriately.

#### Flowise
Flowise container with PostgreSQL database and access to pgvector, redis and opensearch as tools.

###### Configuration files
- `.env_files/.flowise.yaml`: General configurations,
- `.env_files/.flowise.secrets.yaml`: Flowise password.

###### Defaults
Default username/password: `aistack/blablatester0`  

## Limitations
- OpenSearch still needs authentication,
- PostgreSQL pgvector seems not to work very well with some flowise flows.
- Redis seems to throw errors when used for anything else than cache. 

## Disclaimer
Made for fun, to explore. A lot of security aspects have been completely ignored.  
GPL V3 license.

## See also
LocalAI - https://localai.io/  
FlowiseAI - https://flowiseai.com/
PostgreSQL - https://www.postgresql.org/
Redis - https://redis.io/
OpenSearch - https://opensearch.org/

