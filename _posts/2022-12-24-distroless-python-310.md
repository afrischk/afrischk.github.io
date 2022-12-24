---
layout: post
title: Distroless Python Images With Docker
published: true
---

Lately I needed to reduce the attack surface of a Python 3.10 Docker image to a bare minimum. I searched the web for best practices and came across [distroless Docker](https://github.com/GoogleContainerTools/distroless) images. As time of my investigations the Python version on the provided distroless image was 3.9.

After gathering enough information in various blog posts I have created a `Dockerfile` to serve my Python application on a distroless Docker image.

```Dockerfile
FROM python:3.10-slim as base

ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONFAULTHANDLER 1

FROM base AS python-deps

RUN pip install pipenv

COPY Pipfile .
COPY Pipfile.lock .

# create virtual environment
RUN PIPENV_VENV_IN_PROJECT=1 pipenv install --deploy

#https://github.com/GoogleContainerTools/distroless/issues/973#issuecomment-1065169760
FROM gcr.io/distroless/base-debian11:nonroot

WORKDIR /app

ARG ARCH=x86_64-linux-gnu

# copy necessary system libs
COPY --from=python-deps /lib/${ARCH}/libz.so.1 /lib/${ARCH}/libz.so.1

# copy python 3.10 and relevant libs
COPY --from=python-deps /usr/local/bin/python3.10 /usr/local/bin/python3.10
COPY --from=python-deps /usr/local/lib/libpython3.10.so.1.0 /usr/local/lib/libpython3.10.so.1.0
COPY --from=python-deps /usr/local/lib/python3.10 /usr/local/lib/python3.10

# copy packages
COPY --from=python-deps /.venv/lib/python3.10/site-packages /app/site-packages

# set envs
ENV PYTHONPATH /app/site-packages
ENV LD_LIBRARY_PATH /usr/local/lib

# copy program
COPY main.py .

EXPOSE 8080
ENTRYPOINT ["python3.10", "main.py"]
```

You can also find this `Dockerfile` [here](https://gist.github.com/afrischk/9786927329811b7af75ab3fbdf4edcdd).
You might notice that the distroless Python image is makred as 'experimental'.
You can find a discussion in the [issues section](https://github.com/GoogleContainerTools/distroless/issues/973#issuecomment-1065169760) of the GitHub repostiory. 

For me that is not an issue as I was able to reduce the attack surface rigorously.
