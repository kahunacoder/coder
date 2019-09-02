# Microsoft Visual Code in a browser.

(DISCLAIMER! This image is based on the Docker image from [https://github.com/acondura/code-server](https://github.com/acondura/code-server), but heavily stripped down, modified and using `debian:stretch-slim` instead of ubuntu as a base image to decrease the size of it.)

This is a proof of concept of using Visual studio code in a browser, but also of using it completely as your work station with a terminal with ZSH. My thought here is to never even mount your code locally on your machine, but only use one or multiple docker volumes to share code between docker containers, this would then circumvent the speed issues in mounting files locally when using Docker on Mac or Windows.

## Docker compose
The docker compose in this file is mostly for local testing, do not use ut, examples of how you can use Coder practicly will come.