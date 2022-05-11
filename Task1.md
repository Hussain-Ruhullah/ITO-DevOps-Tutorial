goto www.labs.play-with-docker.com

create an html file:
`touch index.html`

and create a simple page:
`<h1> Hello World! </h1>`

now create a Docker file:
`touch Dockerfile`

then type:
`vi Dockerfile`

and define a dockerfile as shown below:
`
 FROM nginx:latest

 COPY index.html /usr/share/nginx/html

 EXPOSE 80     

 CMD ["nginx", "-g", "daemon off;"]
 `
build your image:
`docker build . -t <image-name>`

run your new image 
` docker run -d -p 80:80 <image-name> `
