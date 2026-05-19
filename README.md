This is a guide (and test) for getting started with advanced debugging with cortex-debug extension which allows you to debug things like registers values etc directly within VSCode, bcuz the default CubeIDE extension doesn't allow that much like the true CubeIDE.

To get started, ensure that cubemx and OpenOCD are installed, download cubemx from ST's site. Read the guide for downloading OpenOCD in the repo. (If not, installing them is also in the complete guide from scratch file, that includes the complete start-to-finish setup, ensure that you don't have OCD :D ).

For the complete workflow, read the complete guide from scratch file. 
Save the launch, tasks, and settings JSON files if you like.
Read the documentation for the cortex debug extension for more info on how it works and how to use it.

Next time, you just need to edit the required JSON files in the vscode folder (if all paths and settings for the config are correct).
Also, you need to edit the files with the correct uC you using.

Now, you are ready to debug STM32s professionally in VSCode !
