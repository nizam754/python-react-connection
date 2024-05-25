# python-react-connection
React frontend application and a Python API backend application, both containerized using Docker and demonstrating connectivity between the two.

## React Frontend Application
1. Create a React application:
    ``` bash
    npx create-react-app react-app
    cd react-app
    ```
2. Modify the React application to fetch data from the backend:
   Edit `src/App.js`:
    ``` javascript
    import React, { useState, useEffect } from 'react';

    function App() {
    const [data, setData] = useState(null);

    useEffect(() => {
        fetch('http://localhost:5000/data')
        .then((response) => response.json())
        .then((data) => setData(data));
    }, []);

    return (
        <div className="App">
        <header className="App-header">
            <h1>React Frontend</h1>
            {data ? <p>Data from backend: {data.message}</p> : <p>Loading...</p>}
        </header>
        </div>
    );
    }

    export default App;
    ```
3. Create a Dockerfile for the React app:
Create a Dockerfile in the root of the `react-app` directory:
    ``` Dockerfile
    # Use an official Node runtime as a parent image
    FROM node:14

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in package.json
    RUN npm install

    # Make port 3000 available to the world outside this container
    EXPOSE 3000

    # Run npm start when the container launches
    CMD ["npm", "start"]
    ```

## Python API Backend Application

1. Create a new directory for the Python API:
    ``` bash
    mkdir python-api
    cd python-api
    ```
2. Create a Python virtual environment and install Flask:
    ``` bash
    python3 -m venv venv
    source venv/bin/activate
    pip install flask flask-cors
    ```
3. Create the Flask application:
Create a file named `app.py`:
    ``` python
    from flask import Flask, jsonify
    from flask_cors import CORS

    app = Flask(__name__)
    CORS(app)

    @app.route('/data', methods=['GET'])
    def get_data():
        return jsonify(message='Hello from the backend!')

    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=5000)
    ```
4. Create a `requirements.txt` file:
    ``` txt
    Flask
    Flask-Cors
    ```
5. Create a Dockerfile for the Python API:
Create a `Dockerfile` in the root of the `python-api` directory:
    ``` Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:3.8-slim

    # Set the working directory in the container
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt

    # Make port 5000 available to the world outside this container
    EXPOSE 5000

    # Run app.py when the container launches
    CMD ["python", "app.py"]
    ```
## Docker Compose Configuration
To manage both containers easily, create a `docker-compose.yml` file in the root directory:
``` yml
version: '3.8'

services:
  frontend:
    build: ./react-app
    ports:
      - '3000:3000'
    depends_on:
      - backend

  backend:
    build: ./python-api
    ports:
      - '5000:5000'
```
### Running the Applications
1. Navigate to the root directory containing `docker-compose.yml`:
    ``` bash
    cd /path/to/your/root/directory
    ```
2. Build and start the containers:
    ``` bash
    docker-compose up --build
    ```

React application should now be accessible at http://localhost:3000 and it should fetch data from the Python backend at http://localhost:5000.

This setup provides how to containerize a React frontend and a Python API backend and establish connectivity between them using Docker and Docker Compose.

## Project Architecture
The file architecture for a project involving a containerized React frontend and a Python Flask backend would look something like this:
```
/python-react connection
│
├── docker-compose.yml
│
├── react-app
│   ├── Dockerfile
│   ├── package.json
│   ├── package-lock.json
│   ├── public
│   │   └── ...
│   ├── src
│   │   ├── App.js
│   │   ├── App.css
│   │   ├── index.js
│   │   ├── index.css
│   │   └── ...
│   └── ...
│
└── python-api
    ├── Dockerfile
    ├── app.py
    ├── requirements.txt
    └── venv
        └── ...
```

- /project-root: This is the main folder containing the entire project, including both the frontend and backend components, along with the Docker configurations.

- docker-compose.yml: This file defines services, networks, and volumes for docker containers. It orchestrates the running of both the frontend and backend containers.

- /react-app: This directory houses the React application.

  - Dockerfile: Contains all commands, in order, needed to build the React image.
  - package.json and package-lock.json: Define the project dependencies and lock them to specific versions.
  - /public and /src: Include the static and source files for the React application. App.js is where the React component that calls the backend API is defined.
  
- /python-api: This directory contains the Python Flask application.

  - Dockerfile: Contains the setup for the environment required to run the Flask application.
  - app.py: The main Python file with Flask routes defined for API endpoints.
  - requirements.txt: Lists all Python dependencies.
  - /venv: Virtual environment directory for Python packages (usually not included in the repository or Docker container).

## Component's functionality and the connectivity Testing:
1. Check Container Status
    
    First, ensure that both containers are up and running:
    ``` bash
    docker-compose ps
    ```
    This command will list all the containers managed by docker-compose and show their status. Ensure that the status indicates they are running.

2. Access the React Application
   
   Open a web browser and go to:
   ```
   http://localhost:3000
   ```
   This should load the React application. If the React app is properly set up to fetch data from the backend, it should display data received from the Flask API or indicate it is loading data.

3. Test the Flask API Directly
   
   You can test the Flask API directly to ensure it's running correctly by using a tool like curl or any API client like Postman.

   Using curl:
   ``` bash
   curl http://localhost:5000/data
   ```
   This command should return a JSON response from the Flask application, something like:
   ``` json
   {"message": "Hello from the backend!"}
   ```
   If see the expected message, it means the Flask API is working correctly.

4. Check Logs for Errors
   
   If either application does not respond as expected, check the logs for any errors:
   ``` bash
   docker-compose logs
   ```
   This will display the logs for all services. You can look specifically at the logs for the frontend or backend by specifying the service name:
   ``` bash
    docker-compose logs frontend
    docker-compose logs backend
    ```
5. Check Network Connectivity
   
   Make sure that your React application is correctly pointing to the Flask backend. In your React code (App.js), the API URL should match the Flask service's address. In a development environment managed with Docker Compose, this should typically be http://localhost:5000/data when accessed from your host machine, but it might differ if configured otherwise.

    Final Check

    After going through these steps, if both applications are loading correctly and the React app displays data fetched from the Flask backend, our setup is functioning correctly. If there are any issues, the logs and direct API checks should give you a good indication of what might be wrong.






