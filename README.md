# Learn to use Poetry and Docker 
## Build random-wikipedia-article application using poetry
- Install poetry
    > pip install poetry==1.7.1
- Creating a Project
    > poetry new --src random-wikipedia-article
- Update the required metadata details in the pyproject.toml's tool.poetry section
- Add the required packages pattern to include in the distribution
    > packages = [{include = "random_wikipedia_article", from = "src"}] in tool.poetry section  
    > include = ["tests"] ## optional to include tests in distribution
- Add the required source code modules   
- Add the main function to call in `__main__`.py  
- Manage Dependencies  
    > poetry add rich  
    > poetry add "httpx>=0.27.0" --extras=http2  
- The project requires a recent Python version, so it doesn’t require the importlib-metadata backport. If you had to support Python versions before 3.8    
    > poetry add "importlib-metadata>=6.7.0" --python="<3.8"  
- To check dependencies   
    > poetry show --tree  
- Lock the poetry dependencies
    > poetry lock --no-update      
- Install the project  
    > poetry install  
- Validate the program is running correctly  
    > poetry run random-wikipedia-article

- Add any additional dependencies for creating tests,documentation , check poetry documentation on how to use groups 
    > poetry add --group=tests pytest pytest-sugar  
    > poetry add --group=docs sphinx

- To deploy project to virtual envs , we can use poetry plugins  
    > pip install poetry-plugin-bundle

- Using python bundle to deploy the application into venv
    > poetry bundle venv --python=`<pythonpath>` --only=main `<destination_location>`  
    > poetry bundle venv --python=C:\Users`\<user>\`AppData\Local\Programs\Python\Python311\python.exe --only=main D:\py_deploy\app
- Test deployed application  
    > d:\py_deploy\app\Scripts\activate
    > random-wikipedia-article
## Create docker file 
- Copy the below contents to Dockerfile
    FROM debian:12-slim AS builder  
    RUN apt-get update && \  
        apt-get install --no-install-suggests --no-install-recommends --yes pipx  
    ENV PATH="/root/.local/bin:${PATH}"  
    RUN pipx install poetry  
    RUN pipx inject poetry poetry-plugin-bundle  
    WORKDIR /src  
    COPY . .   
    RUN poetry bundle venv --python=/usr/bin/python3 --only=main /venv  
      
    FROM gcr.io/distroless/python3-debian12 
    COPY --from=builder /venv /venv  
    ENTRYPOINT ["/venv/bin/random-wikipedia-article"]  

- Build the application  
    > docker build -t random-wikipedia-article .    
- Run the application
    > docker run --rm -ti random-wikipedia-article

 



