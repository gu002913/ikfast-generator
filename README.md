## OpenRAVE-IKFAST-Generator

```cpp
//////////////////////////////////////////////////////////////////////////
///  \author  Gu Dingyi
///
///  \file    README.md
///
///  \brief   OpenRAVE-IKFAST-Generator
///
///  \version 1.0
///
///  \date    Apr/03/2024
//////////////////////////////////////////////////////////////////////////
```

- Getting Started with `OpenRAVE` environment. Guidance could be followed at [Stéphane Caron's blog](https://scaron.info/robotics/getting-started-with-openrave.html).

- Error may be frequently encountered during compile. Alternatively, `docker image` is recommended.
  
  ### Docker setup

- Check [personalrobotics/ros-openrave](https://hub.docker.com/r/personalrobotics/ros-openrave/tags)
  
  ```bash
  sudo docker pull personalrobotics/ros-openrave:latest
  ```

- Since the image is pre-built, no need to `docker build` with `Dockerfile`. Just run it. Mirror the `/root/data` to your current workspace.
  
  ```bash
  sudo docker run -it --rm -v /home/dingyi/workspace/dingyi-code/side-project/openrave-test:/root/data --network host ada6b60e98b7 bash
  ```

- Where image id `ada6b60e98b7` could be obtained using `sudo docker images`.
  
  ### Closed-form solver generation

- Now go to `\root\data` in the docker image will direct to the same workspace.

- `xml`file like `ur5.robot.xml` represents the robot model. Use `ikfastpy` to generate the `ikfast solver`.
  
  ```bash
  python `openrave-config --python-dir`/openravepy/_openravepy_/ikfast.py --robot=ur5.robot.xml --iktype=transform6d --baselink=0 --eelink=6 --savefile=ikfast61.cpp --maxcasedepth 1
  ```

- It takes several minutes to generate the closed-form `ikfast solver`, which is a `cpp` file named by you. Then `ikfast61.cpp` is shown in your workspace.

- Now you can `exit` the docker image.
  
  ### Test the solver

- Compile the `ikfast solver` using `gcc`(need `-llapack`).
  
  ```bash
  gcc -fPIC -lstdc++ -DIKFAST_NO_MAIN -DIKFAST_CLIBRARY -shared -Wl,-soname,libik.so -o libik.so ikfast61.cpp
  ```

- `libik.so` comes out in your workspace.

- Compile the executable demo for testing purpose.
  
  ```bash
  g++ ikfastdemo.cpp -lstdc++ -llapack -o compute -lrt
  ```

- Run `compute` for easy tests for `fk` and `ik`.

```bash
./compute fk -3.0, -1.6, 1.6, -1.6, -1.6, 0.0
./compute ik 0.457016 0.172972 0.434512 -0.001461 0.755053 -0.655338 0.020595
```

- Verify your results with the real robot.
