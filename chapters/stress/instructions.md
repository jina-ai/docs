### Instructions for replicating Jina cloud benchmark

You can reproduce this benchmark yourself. You have to do the following:

1. Configure and start the instances in AWS

    You can try using another provider. Additionally, you can try running it in Docker Compose, on your local machine. However, the Annoy & Faiss indexers use their own Docker images and running a Docker within Docker is currently not supported.

1. On all the machines, apart from the client: 
   
    1. Install Jina, JinaD and the other requirements on each of the machines

        The `requirements.txt` files can be found in the folders `image` or `text`. Install the ones you plan to run.
  
1. Do the following in the client machine:
    
    1. clone the [repository](https://github.com/jina-ai/cloud-ops/)
    1. `cd` into the `stress-example` directory
    1. set up the configuration options in `.env`
    1. run the automatic script: `nohup bash run_test.sh > output.txt &`
    
        `nohup` allows you to disconnect from the machine without interrupting the process. The output will be piped into `output.txt`. 

When the process is done, you will find the results in `output.txt`. Look for the lines starting with `TOTAL: Ran operation`

##### Testing locally with Docker Compose

1. Clone the Jina repository from [here](https://github.com/jina-ai/jina/)
   
    Keep track of where you cloned this repo in an env. var., `PATH_TO_JINA_CORE_REPO`.

1. Clone this repository from [here](https://github.com/jina-ai/cloud-ops/)
1. `cd cloud-ops/stress-example`
1. Build docker image `docker build -f debianx.Dockerfile -t stress_example_image_search $PATH_TO_JINA_CORE_REPO`
1. Create docker compose containers `docker-compose -f docker-compose.yml --project-directory . up --build -d`
1. Proceed as normal with configuring the machines and the client, as above.
1. Tear down containers: `docker-compose -f docker-compose.yml --project-directory . down`

