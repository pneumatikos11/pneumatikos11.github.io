You would need to get the installer packages from the MATLAB website. 

>[!Note]
>One problem you might face would be when your directory has a folder name with spaces. You would have to move the default folder elsewhere. 
>

Use the following command to change the folder to avoid theses errors. 
```c
setenv('ARDUINO_STATIC_LIB_PATH',<PATH_TO_FOLDER_NO_SPACES>)
```

# Setting up Simulink
- To begin any Simulink session, you would have to setup up for arduino

1. Go to Model Settings![[Pasted image 20231112204740.png]]
2. Under Hardware Implementation on the left sidebar, select Arduino Uno (or the board you are using)![[Pasted image 20231112204901.png]]
3. Change the stop time to 'inf'
4. From here you can click Build, Deploy and & Start when you have your Simulink model ready