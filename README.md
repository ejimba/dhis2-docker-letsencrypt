# DHIS2 Docker Project

This project sets up a DHIS2 instance with a Postgres database using Docker.

## Project Structure

```
.
├── data/
│   ├── .gitignore
│   ├── dhis/
│   │   └── dhis.conf
│   └── postgres/
└── docker-compose.yml
```

## Prerequisites

Before you can run this project, you need to have Docker and Docker Compose installed on your machine.

- [Install Docker](https://docs.docker.com/get-docker/)
- [Install Docker Compose](https://docs.docker.com/compose/install/)

## Setup

1. Clone this repository.
2. Navigate to the project directory.

## Running the Project

To start the project, run the following command in the project directory:

```sh
docker-compose up
```

This will start the DHIS2 and Postgres containers. The DHIS2 instance will be accessible at `http://localhost:8080`.

## Stopping the Project

To stop the project, run the following command in the project directory:

```sh
docker-compose down
```

This will stop and remove the DHIS2 and Postgres containers.

## Data Persistence

The `data` directory contains data for the DHIS2 and Postgres containers. The `dhis` directory contains the DHIS2 configuration file (`dhis.conf`), and the `postgres` directory contains the Postgres data. The `dhis.conf` are loaded through environmental variables. Please adjust them accordingly

```dockercompose
version: '3.1'
services:
  dhis2:
    container_name: dhis2
    environment:
      DHIS2_HOME: /DHIS2_home
      JAVA_OPTS: -Xms2048m -Xmx4096m
      TZ: Africa/Nairobi
      POSTGRES_HOST: postgres
      POSTGRES_PORT: 5432
      POSTGRES_DB: dhis2
      POSTGRES_USER: dhis
      POSTGRES_PASSWORD: dhis
      SERVER_HTTPS: off
      SERVER_BASE_URL: http://localhost:8080
    depends_on:
      - postgres
    image: dhis2/core:2.40.2
    networks:
      - dhis2
    ports:
      - 8080:8080
    restart: unless-stopped
    volumes:
      - ./data/dhis:/DHIS2_home

  postgres:
    container_name: postgres
    environment:
      POSTGRES_DB: dhis2
      POSTGRES_USER: dhis
      POSTGRES_PASSWORD: dhis
    image: postgis/postgis:15-3.4
    networks:
      - dhis2
    # ports:
    #   - 5432:5432
    restart: unless-stopped
    volumes:
      - ./data/postgres:/var/lib/postgresql/data

networks:
  dhis2:
    driver: bridge
    name: dhis2
```

## Contributing

Contributions are welcome. Please submit a pull request or create an issue to discuss the changes.

## License

This project is licensed under the MIT License.
