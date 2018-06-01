# human-pose-with-scanner-run

# Tutorial to run [scanner.run](http://scanner.run/) to perform human pose detection

# Setup

## Install docker

- Install `docker-ce`. See [this tutorial](https://www.linux.com/blog/learn/intro-to-linux/how-install-docker-ce-your-desktop)
- Install `nvidia-docker`. See [nvidia repo](https://github.com/NVIDIA/nvidia-docker/)

## Pull my custom docker image: 

```docker pull quanhua92/human-pose```

# Run a docker container with human-pose repo

```
docker run --runtime=nvidia -it -v $(pwd):/app human-pose /bin/bash
```

- This also mounts the current directory in to `/app` folder in the docker. 

# Download some videos and put it in the current directory. For example: [https://www.youtube.com/watch?v=2kphHx5Aack](https://www.youtube.com/watch?v=2kphHx5Aack)


# Example 01: Single video

- In the docker bash, change dir to `pose_detection` folder. There is a sample clip named `sample-clip.mp4`.
```
cd /opt/scanner/examples/apps/pose_detection/

```

- Run the pose detection in 
```
python3 main.py sample-clip.mp4
```

- There will be a progress bar as follow:
```
root@0cdb9e4dfc83:/opt/scanner/examples/apps/pose_detection# python3 main.py sample-clip.mp4 
Detecting poses in video sample-clip.mp4
Using GPUs
  0%|                                               | 1/73 [00:28<34:51, 29.05s/it, tasks=73, jobs=1, workers=1]
```

- The output means: `73` tasks, each task is `64` frames. Time to process each task is `29.05s`. Number of workers `1`.


# Example 02: Multiple video

 - In the docker bash, change dir to `pose_detection` folder. There is a sample clip named `sample-clip.mp4`.
```
cd /opt/scanner/examples/apps/pose_detection/

```

- We will process the sample-clip.mp4 and another file that we downloaded in the `Setup` section, named `waving-360.mp4`.

- Run the pose detection in 
```
python3 main-multi.py sample-clip.mp4 /app/waving-360.mp4 
```

- The output will look like this:
```
root@0cdb9e4dfc83:/opt/scanner/examples/apps/pose_detection# python3 main-multi.py sample-clip.mp4 /app/waving-360.mp4 
Detecting poses in video sample-clip.mp4 /app/waving-360.mp4
Using GPUs
  1%|▍                                  | 2/178 [00:57<1:19:54, 27.24s/it, tasks=176, workers=1, jobs=2]

```

- In the `main-multi.py`, we add 2 mp4 files into the `Database` and create `2 job`. The number of tasks `178` is the sum of 2 video frames. There is still only `1` worker so the time to process is still the same as before. 

- However, `scanner.run` can be scaled to multiply workers as in this link. [http://scanner.run/handbook/kubernetes.html](http://scanner.run/handbook/kubernetes.html). In summary, we will have a `main.py` to define our computation graph. We will have another `master.py` and `worker.py` that we will deploy to Google Cloud or other machines to scale the number of workers. In the `main.py`, we will define the `master` in the `Database` and the scanner.run will take care of the scale problem.