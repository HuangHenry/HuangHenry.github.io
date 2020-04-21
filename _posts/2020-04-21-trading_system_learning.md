---
layout: post
title: MATLAB crash problem
categories: [crash, MATLAB]
description: MATLAB 经常开机死机，或者运行画图时死机
keywords: crash, MATLAB
---

## MATLAB 启动时死机现象

### Problem #4: Issue with Java environment

MATLAB has a command line environment in addition to the normal MATLAB Desktop environment. This environment is not dependent on Java and can be useful for troubleshooting MATLAB. To launch MATLAB with no desktop environment:

1. Right click on the MATLAB shortcut and choose properties.
2. Go to the end of the "Target" line, after MATLAB.exe, and add the flag -nodesktop. For example:
   "C:\Program Files\Matlab\R2016a\bin\matlab.exe" -nodesktop
   Press OK to save these changes.
3. Try starting MATLAB again. If MATLAB starts, try running the command "desktop" to see if MATLAB will open in full mode.
4. If this does not work, add the line -nojvm where -nodesktop previously existed and try to start MATLAB again.
5. Finally, you can use the line **-softwareopengl** and try to start MATLAB again.

6. You can work around many graphics issues by using the software version of OpenGL® built-into MATLAB instead of using your graphics hardware. In some cases, MATLAB automatically switches to software OpenGL, for example, if it detects known issues such as outdated drivers or graphics virtualization. Software OpenGL does not support some graphics features, such as graphics smoothing.

7. To determine if you are using hardware or software OpenGL, type 'opengl info'. The returned information contains the line Software: 'false' if you are using hardware OpenGL or Software: 'true' if you are using software OpenGL.
   To switch from hardware to software OpenGL, start MATLAB from the command prompt or terminal on your system using this command:

    matlab -softwareopengl
To set your preferences so that MATLAB always starts with software OpenGL on this computer, execute this command at the MATLAB command line:
    opengl('save','software')

## Reference
[matlab crash](https://www.mathworks.com/matlabcentral/answers/97167-why-will-matlab-not-start-up-properly-on-my-windows-based-system)