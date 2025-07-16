# Live Link Face CSV Importer for Maya

### My Modification
- CSVImport.vcxproj file
    - when open this vcxproj file with Visual Studio 2022, you will see a window like image vcxproj_sln.jpg shows, and it will
        - Automatic change \<WindowsTargetPlatformVersion\>
        - Automatic change \<PlatformToolset\>
    - For Debug|x64 and Release|x64
        - Change \<IncludePath\> to your PC's path
            - e.g. Original is "E:\maya_devkits\2022\devkitBase\include";
            - My setting is "C:\Autodesk_Maya_2024_DEVKIT_Windows\devkitBase\include";
            - Official DevKit download link: <https://aps.autodesk.com/developer/overview/maya>
        - Change \<AdditionalIncludeDirectories\> as above, my path is "C:\Autodesk_Maya_2024_DEVKIT_Windows\devkitBase\include"
        - Change \<AdditionalLibraryDirectories\>, my path is "C:\Program Files\Autodesk\Maya2024\lib;C:\Autodesk_Maya_2024_DEVKIT_Windows\devkitBase\lib"
        - I removed \<PostBuildEvent\>'s command, so compiled mll will not be moved to another folder, I choose to copy my mll by myself
- csvImport.cpp file
    - In csvImport::get_frame function, after line 11
    ```cpp
    ...
    std::vector<std::string> out = find_frame(time);
    // below are my code
    // none-apple livelink face app format
	if (out.size() == 1)
	{
		return std::stoi(out.at(0));
	}
    // finish my code
    // get the frames
	int output = std::stoi(out.at(3));
    ...
    ```
So I change timecode logic，after find_frame() function returns the timecode in csv file, except Apple LiveLinkFace App HH:MM:SS:Frame.MS format(like 07:44:57:02.944), the face blendshape csv can also write timecode as frame number 1,2,3,4,5,6..., so other face mocap applications (such as my developed project) can write their mocap data into this similar csv with simple interger frame, and can be imported to maya

### Instructions

Simply move the CSVImporter.mll to your plug-ins folder. The path may vary depending on the environment variables setup on your machine. The default however is "C:/Users/Name/Documents/maya/Version/plug-ins", the most common alternative path would be "C:\Program Files\Autodesk\Maya2020\bin\plug-ins". If neither work check your environment variables for MAYA_PLUG_IN_PATH and adjust your path accordingly.

Activate the plug-in in your plug-in manager, select the target mesh and import a .csv through your preferred method. The mesh needs to have the blendshapes setup already, else it will throw an error. The blendshapes also need to have the same name as in the .csv. If you want to change the names of the meshes you can create the blendshapes with the proper names and rename them afterwards. Joints follow the same rules except they can't have their name changed at all. I am considering to add the ability to change joint names in the future. If singular blendshapes/joints are missing the file will still be imported, but your mocap will most likely look odd. For now the Z Axis controls Roll, Y Yaw and X Pitch, I will add the ability to change the axis before import in the future.

Latest compiled release available for Maya 2018-2022 [here](https://github.com/ArhasGH/Live-Link-Face-CSV-to-Maya/releases/latest)

