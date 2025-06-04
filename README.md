# chat-to-3d

An application that combines natural language processing with 3D asset generation using TRELLIS.

## Features

- Interactive chat interface for scene planning
- AI-assisted object suggestion and layout
- Automatic 3D asset generation from text prompts
- Blender auto-import functionality for generated assets
- VRAM management with process termination

## Installation

### Prerequisites
The NIM Prerequisite Installer requires Microsoft User Account Control (UAC) to be enabled.  UAC is enabled by default for Windows, but if it has been disabled, it must be enabled to ensure successful installation of the NIM Prerequisite Installer.  More information on Microsoft UAC can found [HERE](https://support.microsoft.com/en-us/windows/user-account-control-settings-d5b2046b-dcb8-54eb-f732-059f321afe18)

Download the MS Visual Studio Build Tools [vs_buildTools.exe](https://aka.ms/vs/17/release/vs_BuildTools.exe)
Open a command prompt at the vs_BuildTools.exe file location and send the following command:
```
vs_buildtools.exe --norestart --passive --add Microsoft.VisualStudio.Workload.VCTools --includeRecommended
```
Use winget to install Miniconda:
```
winget install miniconda3
```
Download the NVIDIA CUDA Toolkit 12.9
[NVIDIA CUDA Toolkit 12.9](https://developer.download.nvidia.com/compute/cuda/12.9.0/local_installers/cuda_12.9.0_576.02_windows.exe)
Run the installer and select a custom installation.
![Screenshot 2025-05-22 221843](https://github.com/user-attachments/assets/e2e7fe07-d530-4aca-9668-a8566d1d5864)
From the options select ONLY:  
CUDA  >> Development >> Compiler
CUDA >> Runtime >> Libraries
![Screenshot 2025-05-22 222023](https://github.com/user-attachments/assets/9ccd92cc-55a5-467d-b4f3-f1e821a07689)
Complete the installation

Download the [NIM Prerequisite Installer](https://assets.ngc.nvidia.com/products/api-catalog/rtx/NIMSetup.exe), and run the NIMSetup.exe file, and follow the instructions in the setup dialogs. This will install the necessary system components to work with NVIDIA NIMs on your system.

You will need to reboot your computer to complete the installation.

Git is required and should be installed using winget from a command prompt::
```
winget install --id Git.Git 
```

Install Microsoft Visual C++ 2015-2022 Redistributable Package  
[https://aka.ms/vs/17/release/vc\_redist.x64.exe](https://aka.ms/vs/17/release/vc_redist.x64.exe)
or
```
winget install Microsoft.VCRedist.2015+.x64
```
![Untitled-8](https://github.com/user-attachments/assets/29184836-3791-4c22-8a40-3254590faa0e)

This blueprint requires the installation of Blender. The blueprint has been tested with the Blender 4.27 LTS (Long Term Support) build.   
[https://www.blender.org/download/release/Blender4.2/blender-4.2.7-windows-x64.msi](https://www.blender.org/download/release/Blender4.2/blender-4.2.7-windows-x64.msi)

Blender 4.27 can also be installed using winget from a command prompt:
```
winget install --id 9NW1B444LDLW
```

### Create a Miniconda environment
1. From the Windows Start button, open a Anaconda (Miniconda) Prompt
2. ![image](https://github.com/user-attachments/assets/a53c4eb6-fb96-4f72-8cee-3aa12e0a470e)
3. You should see the command prompt with prompt prefixed with the (base) conda environment name
4. Create a dedicated environment for the blueprint
5. ![image](https://github.com/user-attachments/assets/c9161d64-2d9d-4eae-92b7-5277ef8fa872)
6. ```conda create -n trellis python=3.11.9```
7. ![image](https://github.com/user-attachments/assets/e32395e5-bbdb-4c2b-b270-044c1ec21512)
8. Select Y to proceed
9. Once the environment is successfully created, activate the trellis environment
10. ```conda activate trellis```
11. ![image](https://github.com/user-attachments/assets/fc593129-4f6f-4388-8ab6-cb523fefd5c2)
12. The "trellis" environment name will now be prefixed in the command prompt
13. ![image](https://github.com/user-attachments/assets/8500c4ef-6614-461f-82be-9b3a5ac64e22)

### Installation Steps

1. Clone this repository:
```bash
git clone --recursive https://github.com/anmaurya001/chat-to-3d.git

# If you forgot --recursive during clone:
# git submodule update --init --recursive
```

2. Activate the trellis environment
3. ```conda activate trellis```

3. Install dependencies:
#### Update pip and install build tools
```python -m pip install --upgrade pip wheel```
```python -m pip install setuptools==75.8.2```

#### Install requirements
```
pip install -r requirements-torch.txt
pip install -r requirements-other.txt
pip install -r requirements.txt
```
### Copy Blender Add-Ons to the Blender Add On directory
1. From the blender folder of the local repository, copy the files NV_Trellis_Addon.py, and asset_importer.py
2. Copy these files to ```%appdata%\Blender Foundation\Blender\4.2\scripts\addons```
3. Open Blender
4. Open Edit >> Preferences >> Add-Ons
5.![image](https://github.com/user-attachments/assets/0dd045e1-225a-425f-9f96-9047f3ad476a)
6. Enable CHAT-To-3D and Asset Importer by checking the boxes next to the add on names.
7. Open the CHAT-TO-3D add on preferences and set the CHAT-TO-3D base folder to the chat-to-3d local repository directory.
8. ![image](https://github.com/user-attachments/assets/1f9b6bec-dd13-4a7b-9a02-ac9c84a56869)

## Usage - Manual
1. Start the LLM NIM
```bash
cd nim_llm
python run_llama.py
```

2. Start the application:
```bash
cd chat-to-3d-core
set ATTN_BACKEND=flash-attn
set SPCONV_ALGO=native
set XFORMERS_FORCE_DISABLE_TRITON=1
python run.py
```

3. Open your browser to the URL shown in the terminal (typically http://localhost:7860)

4. To terminate the application and free VRAM:
```bash
cd chat-to-3d-core
python terminator.py
```
This will:
- Gracefully terminate the Gradio application
- Free up GPU memory
- Allow you to proceed with other operations (e.g., Blender)

## Usage - Blender Add On
The CHAT-TO-3D Blender add-on can automatically manage the CHAT-TO-3D services without the need to manually start or stop these services outside of Blender.
1. Open Blender
2. In the 3D layout view look for the Add On tabs on the right edge of the viewport, press N if they are not visible
3. ![image](https://github.com/user-attachments/assets/2bfc6cb2-aa3a-4422-b1d1-a983ed21407d)
4. Note: It is recommended to open a system console viewer to monitor the services and any information or errors that may be output.
   a. Blender Menu >> Window >> Toggle System Console
5. Click the Start CHAT-TO-3D button to start the LLM agent, and the Trellis 3D services.
6. ![image](https://github.com/user-attachments/assets/b5391eed-0cca-45da-952a-04381804c0b5)
7. Once all services have successfully started, the service will indicate: READY and the OPEN CHAT-TO-3D UI button will become available


### Using the Interface

Once the application is running (either through manual start or via Blender Add-On), you can:

- Describe your scene
- Get AI suggestions for objects
- Generate 3D assets
- Auto-import into Blender
  

### Blender Integration
- Open Blender
- Click on Scripting and New.
- Paste the script from blender/auto_import.py.
- Click run.
- Note - If you have new assets, rerun it.

## Acknowledgments

- [TRELLIS](https://github.com/microsoft/TRELLIS) for the 3D generation capabilities
- [Griptape](https://github.com/griptape-ai/griptape) for the agent framework
- [Gradio](https://github.com/gradio-app/gradio) for the web interface
- [TRELLIS Windows Installation Guide](https://github.com/ericcraft-mh/TRELLIS-install-windows) for Windows setup instructions

