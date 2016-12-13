# Jupyter container for Literate Computing for Reproducible Infrastructure on Docker

Launch Your Jupyter container to manage your infrastructure...!

You can install and run the container easily in the following steps:

1. Clone or Download this repository
2. Start the container by `docker-compose` command

    ```
    $ cd (this repo)
    $ docker-compose up -d
    ```

3. Open `http://localhost:8888` and create your notebook...!

By default, Jupyter Notebook in the container is configured as below:

- notebooks are stored in `(this repo)/notebooks`
- the service are running on 127.0.0.1 (localhost)

To change these settings, modify `docker-compose.yml` and run `docker-compose up -d` again!
