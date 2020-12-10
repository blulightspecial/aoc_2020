# aoc_2020
Solutions to advent of code 2020

Run in a docker containter. To set it up use:

```shell
docker run -d --rm -v ~/Documents/MIDS:/home/jovyan/work -p 1225:8888 --name santa jupyter/tensorflow-notebook
```

To get the token to login execute:

```shell
docker exec santa jupyter notebook list
```
