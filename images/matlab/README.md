# Dandi Docker Images

This folder contains the Dockerfile to build the MATLAB image for Dandi:

* `Dockerfile` provides a Nebari compatible jupyter environment with MATLAB(R) installed. This image requires you to bring your own licence.

## MATLAB Docker Image

The MATLAB Docker image relies on the [MATLAB Integration for Jupyter in a Docker Container](https://github.com/mathworks-ref-arch/matlab-integration-for-jupyter).
It is shipped with [MATLAB-proxy](https://github.com/mathworks/matlab-proxy) which enables communication with MATLAB from a web-browser, and with [MATLAB-proxy-jupyter](https://github.com/mathworks/jupyter-matlab-proxy) which adds MATLAB integration for Jupyter.

This Dockerfile includes the following package, including platform products, toolbox products, and support packages from MathWorks® obtained via MATLAB Package Manager [(MPM)](https://www.mathworks.com/products/mpm.html), and additional add-on packages obtained from File Exchange and/or GitHub®

| # | Package | Source | Current Version | Projected Update Strategy |
| --- | :--- | :---- | :--- | :--- |
| 1 | MATLAB | MPM | 2026a | Penultimate MathWorks Release |
| 2 | Bioinformatics Toolbox | MPM | 2026a | Penultimate MathWorks Release | 
| 3 | Computer Vision Toolbox | MPM | 2026a | Penultimate MathWorks Release |
| 4 | Curve Fitting Toolbox | MPM | 2026a | Penultimate MathWorks Release | 
| 5 | Deep Learning Toolbox | MPM | 2026a | Penultimate MathWorks Release | 
| 6 | Econometrics Toolbox | MPM | 2026a | Penultimate MathWorks Release | 
| 7 | Financial Toolbox | MPM | 2026a | Penultimate MathWorks Release | 
| 8 | Image Processing Toolbox | MPM | 2026a | Penultimate MathWorks Release| 
| 9 | Optimization Toolbox | MPM | 2026a | Penultimate MathWorks Release |
| 10 | Parallel Computing Toolbox | MPM | 2026a | Penultimate MathWorks Release |
| 12 | Signal Processing Toolbox | MPM | 2026a | Penultimate MathWorks Release |
| 13 | Statistics & Machine Learning Toolbox | MPM | 2026a | Penultimate MathWorks Release |
| 14 | Wavelet Toolbox | MPM | 2026a | Penultimate MathWorks Release |
| 15 | Deep Learning Toolbox Converter for TensorFlow Models | MPM | R2026a | Penultimate MathWorks Release |
| 16 | [MatNWB](https://github.com/NeurodataWithoutBorders/matnwb) | GitHub | v2.9.0 | Latest Release (currently pinned on penultimate release) |
| 17 | [Brain Observatory Toolbox](https://github.com/MATLAB-Community-Toolboxes-at-INCF/Brain-Observatory-Toolbox) | GitHub | v0.9.4.2 | Latest Release |
| 18 | [Deep Interpolation Toolbox](https://github.com/MATLAB-Community-Toolboxes-at-INCF/DeepInterpolation-MATLAB) | GitHub | v0.9.1 | Latest Release |
| 19 | [EXTRACT](https://github.com/schnitzer-lab/EXTRACT-public) | GitHub | Latest Commit | Latest Commit |
| 20 | [CIATAH](https://github.com/bahanonu/ciatah) | GitHub | Latest Commit | Latest Commit |
| 21 | [Example Live Scripts](https://github.com/MATLAB-Community-Toolboxes-at-INCF/example-live-scripts) | GitHub | Latest Commit | Latest Commit |

_Note:_ `EXTRACT` and `CIATAH` pull the latest commit from the git repository at Docker image build time. `Example Live Scripts` however pulls the latest commit from the git repository **the first time you start a MATLAB session** and only this first time. If you already have pulled a version and need to update to a more updated latest commit from the git repository open a console, then:

```bash
cd example-live-scripts
git pull
```

If you have modified the existing examples, you might have to solve conflicts manually.

### How to Build

Building the MATLAB Docker image is straight forward.
The following lines consider that you already cloned the repository and that you are positioned in the `docker` folder in the cloned repository on your file system.

```bash
docker buildx build -f Dockerfile . --tag dandi-matlab
# or 
docker buildx build . -t dandi-matlab
```

This will build the image tagging it as `dandi-matlab`.


### How to Run Locally

<details>
<summary>Warning: Closing your Session</summary>
Be careful while closing your session.
If you don't close the session properly prior to stop your container, _i.e_: closing the MATLAB session and disconnecting yourself, there is chances that the MATLAB licencing system sees yourself as still connected and you'll have to wait the timeout of the session to be able to log/connect again after restarting the container.

To properly close your session, click on the `MATLAB Jupyter Setting` button which appears above the MATLAB top bar.
From there, if you really want to close your session, clic on "Stop MATLAB Session", and if you really want to stop your Jupyter session, clic on "Sign Out".
</details>

The Dockerfile is tailored to be used through Nebari in the DandiHub platform, but there is a way to launch the image locally without having to rely on a local Nebari setup. Running the image locally implies two main constraints: 

1. you need to modify the Dockerfile to comment the 2 last lines related to the `ENRYPOINT` and `CMD`;
2. if you want to keep your data between multiple rebuilds of the Docker image, you need to mount yourself a shared volume from your local file system.

Once you modified the Dockerfile by commenting the two last lines, you can run the image. 
Running a container for the built image requires that a port is passed to the command line to tell the container which internal port needs to be exposed and on which port to map it in the host system. 
Please note that the following command doesn't mount a shared volume.

```bash
docker run -p 8888:8888 dandi-matlab:latest
```

This command considers the exposition of port `8888` and maps it to the port `8888` in the host.
The syntax of the option is `-p [host port]:[container port]`.
The port to expose in the container is always `8888`, but the host port can be changed to what is the best for your system.

After the container started, you can check the logs and you will see lines giving you the address you can open in your web browser to start the Jupyter instance.

```
To access the server, open this file in a browser:
    file:///home/jovyan/.local/share/jupyter/runtime/jpserver-6-open.html
Or copy and paste one of these URLs:
    http://78bd0f342a19:8888/lab?token=6bf3ad4d468ab3532fab610f5ff28dcf27b1b60300ec8e0c
 or http://127.0.0.1:8888/lab?token=6bf3ad4d468ab3532fab610f5ff28dcf27b1b60300ec8e0c
```

To open locally the Jupyter, copy/paste the `127.0.0.1:8888/xxxxx` address in your browser.

CAUTION: If you changed the port on which will be mapped the internal container port, do not forget to change it also in the address you copy/paste from the logs.


### Add new Add-Ons

By default, the `Dockerfile` image is shipped with two addons already installed and accessible from MATLAB.
You can easily add/remove addons by changing some lines in the Dockerfile: the addons links to download/install are defined by the `ADDONS_*` variables.

CAUTION: The download links have to be release links towards `.zip` files.

#### How the Add-On Registration is Working

The add-ons registration is actually performed in two steps happening at two differents times: at "docker image construction" time, and at MATLAB startup time.

During the docker image construction, all add-ons referenced by the `ADDONS` variable in the Dockerfile are downloaded and extracted in a specific folder: `/opt/extras/dandi`.

At startup-time, this folder is automatically scanned by MATLAB and all downloaded add-ons are added to the "path" of MATLAB.
The code responsible for the auto-scan of the add-ons folder is directly injected in the `startup.m` file during the docker image construction.
If some add-ons require extra actions after being installed/added to the path, you can modify these lines to add extra action before the `clear` at the end of the script injection in the Dockerfile.


### Customize your Container

You can customize some parameter of your container changing some variables in the `Dockerfile`.

You can impact those parameters:

`ADDONS_DIR`:
This variable defines where the add-ons must be downloaded/extracted and what will be the folder scanned by MATLAB at startup time.
If you change this folder, the Jupyter user needs to have read/write access to it. This comes from a specificity of `matnwb` which requires the execution of some extra actions for its activation.

`ADDONS_RELEASES`:
This variable defines the list of add-ons to download and install. You can add as much add-ons as you want as long as they are compatible with MATLAB-R25.

`ADDONS_LATEST`:
This variable defines the list of add-ons to download and install directly from the lastest version identified in the github repository.
