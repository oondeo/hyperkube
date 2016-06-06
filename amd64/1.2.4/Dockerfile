FROM gcr.io/google_containers/hyperkube-amd64:v1.2.4


RUN apt-get update && apt-get install paxctl \
  && paxctl -Cm /hyperkube && rm -rf /var/lib/apt /var/cache/apt
