# Day 2/40 - How To Dockerize a Project - CKA Full Course 2024 ☸️
**some important shortcut of vi editor**
✅ Delete the current line

Make sure you are in Normal mode
Press Esc (once or twice to be sure)
Press:dd
👉 This deletes the entire line where the cursor is.


✅ Delete multiple lines
Delete N lines starting from current line:
5dd
(Deletes 5 lines)


✅ Delete a specific line number
:10d
Deletes line 10
Delete a range:
:10,20d
Deletes lines 10 to 20


✅ Delete all lines (clear file)
:%d

🧠 Useful tip
Deleted lines go into the buffer, so you can paste them back with:
p



Delete current line	dd
Delete N lines	Ndd
Delete specific line	:Nd
Delete all	:%d


undo (u)

copy line (yy)

paste (p)

search (/text)

## Check out the video below for Day2 👇

[![Day 2/40 - How To Dockerize a Project - CKA Full Course 2024](https://img.youtube.com/vi/nfRsPiRGx74/sddefault.jpg)](https://youtu.be/nfRsPiRGx74)

## If you would like to use docker and Kubernetes sandbox environment , you can use below:
```
https://labs.play-with-docker.com/
https://labs.play-with-k8s.com/
```

## Download Docker desktop client
```
https://www.docker.com/products/docker-desktop/
```

## Get started
- Clone a sample git repository using the below command or use your project for the demo:

```
git clone https://github.com/docker/getting-started-app.git
```

- cd into the directory
```
cd getting-started-app/
```
- Create an empty file with the name Dockerfile
```
touch Dockerfile
```
- Using the text editor of your choice, paste the below content ( Details about each of these have already shared in the video)
```
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

- Build the docker image using the application code and Dockerfile

```
docker build -t day02-todo .
```
- Verify the image has been created and stored locally using the below command:
```
docker images
```

- Create a public repository on hub.docker.com and push the image to remote repo
```
docker login
docker tag day02-todo:latest username/new-reponame:tagname
docker images
docker push username/new-reponame:tagname
```

- To pull the image to another environment , you can use below command
```
docker pull username/new-reponame:tagname
```

- To start the docker container, use below command

```
docker run -dp 3000:3000 username/new-reponame:tagname
```

- Verify your app. If you have followed the above steps correctly, your app should be listening on localhost:3000
- To enter(exec) into the container, use the below command

```
docker exec -it containername sh
or
docker exec -it containerid sh
```
- To view docker logs

```
docker logs containername
or
docker logs containerid
```



  
