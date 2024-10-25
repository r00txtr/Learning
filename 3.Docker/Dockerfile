FROM training.btech.id/btacademy/docker-http-server:health
HEALTHCHECK --interval=1s --retries=3 \
    CMD curl --fail http://localhost:80/ || exit 1

