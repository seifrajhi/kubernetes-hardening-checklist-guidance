#Appendix A: Dockerfile example for non-root application

The following example is a Dockerfile that runs an application as a non-root user and non-group member.

```docker
FROM ubuntu:latest
# Upgrade and install the make tool
RUN apt update && apt install -y make
# Copy the source code from a folder called code and build the application using the make tool.
COPY ./code
RUN make /code
# Create a new user (user1) and a new group (group1); then switch to the context of the user.
RUN useradd user1 && groupadd group1
USER user1:group1
#Set the default entry for the container
CMD /code/app
```

