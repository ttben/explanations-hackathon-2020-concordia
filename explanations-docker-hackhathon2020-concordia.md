
# Summary
Here is the overall point-of-view of what you will end up with :)
A folder named as the targeted language (here `elixir` and another folder for `erlang`).
The language folder contains two files (`Dockerfile` and `build-and-run.sh`) and a folder (`data`).
The folder `data` contains all the files that you need to run your example, and `start.sh` specify *how* to start your example. Some languages can have fancy way to run one of their program, and you want to hide this, encapsulate it.

![Recap](https://user-images.githubusercontent.com/6167712/81432649-96710300-9163-11ea-9df0-91779cad867e.png)


# Write a dockerfile
The Dockerfile will copy, add, download, do everything that needs to be done to have a properly set-up environment to run a program in your targeted language.

- Easy way: Look for `dockerhub XXX` where `XXX` is the langage you want to work with. It is most likely that you will find a docker image in dockerhub that exists
- Hard way: if you can't find such Dockerfiles, you will need to wrap the runtime and all that is needed to run your program manually. I can help with that :). 

In my case, I had to use `elixir`, and found [this docker image](https://hub.docker.com/_/elixir). **Sweet \o/**
To avoid to execute the command the image `elixir` specified (that starts the elixir shell), I wrote a dockerfile that :

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

