The goal is to wrap your contribution into a Docker image in order to quickly and easily run your work, without having to set-up the environment. Imagine if you want to try contributions from someone and need to install 67,890,987,650 modules/binaries/packages, modify your `path` etc. And we have many contributions.

Nope. _Nope_. **Nope**.

The idea is to wrap everything in a dockerfile and have two scripts: one that builds the docker image from your dockerfile, the other that speciies what needs to be run when one runs your docker image.

# Summary
Here is the overall point-of-view of what you will end up with :)
- A folder named as the targeted language (in my case `elixir` and another folder for `erlang`).
- The language folder contains two files (`Dockerfile` and `build-and-run.sh`) and a folder (`data`).
- The folder `data` contains all the files that you need to run your example (script files, resources, whatever), and `start.sh` specify *how* to start your example. Some languages can have fancy way to run one of their program, and you want to hide this, encapsulate it.

You will find the [hierarchy inside this repository](https://github.com/ttben/explanations-hackathon-2020-concordia/tree/master/language). You use this repository as a base ;).

![](https://user-images.githubusercontent.com/6167712/81434417-64ad6b80-9166-11ea-8963-62fa18ee4c85.png)

# Write a dockerfile
The Dockerfile will copy, add, download, everything that needs to be done to have a properly set-up environment to run a program in your targeted language.

- Easy way: [Look for `dockerhub XXX`](https://lmgtfy.com/?q=dockerhub+rust) where `XXX` is the langage you want to work with. It is most likely that you will find a docker image in docker hub for your language.
- Hard way: if you can't find such docker image, you will need to manually wrap the runtime and all that is needed to run your program. I can help with that :). 

In my case, I had to use `elixir`, and found [this docker image](https://hub.docker.com/_/elixir). **Sweet \o/**
To avoid to execute the command that the image `elixir` specified (here it starts the elixir shell), I wrote a dockerfile that :

- 1) extends the `elixir` image, that does the black-magic to set-up a functionning elixir environment,
- 2) adds the necessary files for my program to work,
- 3) specifies the script that will be run, once one starts the image

Here is an example of a Dockerfile that uses `elixir`, copies whatever is inside the local `data` folder into a `/data` folder inside the final docker image, and specify the script `./start-elixir-script.sh` that need to be run when one start the image. **This is a generic dockerfile and can easily be tuned to match your needs**.

```
FROM elixir

COPY data/ /data
WORKDIR /data

ENTRYPOINT ./start.sh
CMD /bin/bash
```

# Write the start script

This is most likely straightforward and will depend on the language you are using.
In my case, I just need to call the `elixir` executable that was added by the docker image `elixir` that I extended.
Here is the content:
```
elixir hackhaton_2020_elixir.exs
```

# Write a tiny helper

I wrote a `build-and-run.sh` script that build my image, and starts it. This is really basic, but by doing so, **to reproduce my results, you just have to get my files, type `./build-and-run.sh`, and that it is**. The docker image is built, the environment set-up, the program started \o.


```
docker build . -t elixir-custom 
docker run elixir-custom
```

