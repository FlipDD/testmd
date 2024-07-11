# Switch porting process

## Requirements
- SDK setup
- Lot Check
    - Authoring Tool
    - FsAccessLogChecker
- Config
    - Set startup account
    - [Icons, meta artwork](https://github.com/kf-jbialo/porting-reference/wiki/Platform-Reference-Images)
    - Legal info
        - App ID
        - Ratings
    - Location
        - Unity - Project Settings > Player > Nintendo Switch Settings > Publishing Settings
        - Unreal - Project Settings > Platforms > Switch
- Controller
    - Menu navigation
    - Swap A/B and X/Y
    - Compliant button prompts
        - Pro Controller D-pad uses different icons
    - Controller disconnection
        - Using the applet can be an issue in multiplayer games since it stalls the main thread
    - Controller vibration
        - Make sure it doesn’t vibrate too long. Try to pause after vibration is triggered
- Save system
    - Asynchronous
    - Limit save size
        - Save data size: total size. Limit it to 64mb
        - Journal size: max size of an individual save file (each file is 32kb by default)
        - Once you calculate all of these sizes, the number you plugin have to be multiple of 16384 (16kb)
        - Set it in the [config settings](https://www.notion.so/Astrea-fd128e79d1f24b359db404a2bb5dca92?pvs=21)
- Hide innapropriate items
    - Hide items like Quit, Graphics settings, Join Discord, etc
    - Use icons for button prompts
- [Terminology](https://www.notion.so/Astrea-fd128e79d1f24b359db404a2bb5dca92?pvs=21)
    - “HD Rumble” instead of vibration
- Auto language detection
- ROM compression
- No long hitches
- No System.io methods
- Leaderboards
    - Requires NEX compliance
- CPU Boost Mode
    - Remember there’s a time limit. Link to docs
- Addressables
    - Not a requirement but probably need it 90% of the times
- Performance
    - Unreal
        - [Oodle texture compression](https://docs.unrealengine.com/4.27/en-US/TestingAndOptimization/Oodle/Texture/)
        - Platform specific min LOD (possibly with cheaper materials), VFX scalability, SwitchDeviceProfiles.ini
- Some bugs that may be Switch specific due to its architecture and use of Forward Rendering: Reflection Captures, Volumetric Clouds.

## Useful commands

<details>
	<summary>Make builds</summary> 
	<p>smt smt</p>
	<details>
		<summary>Unreal</summary>
	    	<pre><code>
@echo on
title Building GameName on Switch (Shipping)
echo Starting a Switch build...
C:/Unreal/GameName/4.27_v2/Engine/Binaries/DotNET/AutomationTool.exe BuildCookRun -project=C:/Unreal/GameName/Game/ProjectName.uproject -noP4 -clientconfig=Shipping -serverconfig=Shipping -nocompile -nocompileeditor -installed -ue4exe=C:\Unreal\GameName\4.27_v2\Engine\Binaries\Win64\UE4Editor-Cmd.exe -utf8output -platform=Switch -build -cook -map=+Map1+Map2+Map3 -unversionedcookedcontent -compressed -stage -package -stagingdirectory=M:/Builds/GameName/BuildMachine/v2/ -cmdline=""
echo Finished building for Switch.
		</code></pre>
	</details>
</details>

<details>
	<summary> Patching </summary> 
	<details>
		<summary> Analyze patch </summary>
		<pre><code>
%NINTENDO_SDK_ROOT%\Tools\CommandLineTools\AuthoringTool\AuthoringTool.exe analyze-patch C:\GameName\Binaries\Switch\GameName-Switch-Shipping-patch.nsp --previous C:\GameName\Releases\Cert\Switch\LatestPatch\GameName-Switch-Shipping-Update-Patch2-RC1.nsp --original C:\GameName\Releases\Cert\Switch\OriginalRelease\GameName-Switch-Shipping.nsp
		</code></pre>	
   	</details>
	<details>
		<summary> Diff patch </summary>    
	        <pre><code> 
%NINTENDO_SDK_ROOT%\Tools\CommandLineTools\AuthoringTool\AuthoringTool.exe diffpatch C:\GameName\Releases\Cert\Switch\OriginalRelease\GameName-Switch-Shipping.nsp C:\GameName\Releases\Cert\Switch\LatestPatch\GameName-Switch-Shipping-Update-Patch2-RC1.nsp C:\GameName\Binaries\Switch\GameName-Switch-Shipping-patch.nsp
	 	</code></pre>
 	</details>
    	<details>
		<summary> Compare NSP </summary>
		<pre><code>
%NINTENDO_SDK_ROOT%\Tools\CommandLineTools\AuthoringTool\AuthoringTool.exe comparensp C:\GameName\Binaries\Switch\GameName-Switch-Shipping-patch.nsp C:\GameName\Releases\Cert\Switch\OriginalRelease\GameName-Switch-Shipping.nsp
		</code></pre>
	</details>
</details>

<details>
	<summary> Filesystem </summary> 
	<details>
		<summary> Copy files from the SD card to PC </summary>
		<pre><code>
%NINTENDO_SDK_ROOT%\Tools\CommandLineTools\RunOnTarget.exe %NINTENDO_SDK_ROOT%\TargetTools\NX-NXFP2-a64\DevMenuCommand\Release\DevMenuCommand.nsp -- debug copy --source sdcard:/ --destination M:/nx_sdcard --skip-error-file
		</code></pre>	
   	</details>
	<details>
		<summary> Restore/Backup Save Data </summary>
		<pre><code>
[KF Documentation](https://github.com/kf-jbialo/porting-reference/wiki/Console-Storage-Backup-Restore))
		</code></pre>	
   	</details>
</details>

<details>
	<summary> FsAccessLogChecker </summary>
	<pre><code>
	%NINTENDO_SDK_ROOT%\Tools\FsAccessLogChecker\FsAccessLogChecker.exe M:\nx_sdcard\FsAccessLog.txt -o M:\nx_sdcard\FsAccessLog-Result.txt
	</code></pre>	
</details>

<details>
	<summary> Initialize EDev manually </summary>
	<p>Fixes the kit not booting due to failing to update the firmware through Nintendo Dev Interface</p>
	<pre><code>
	`%NINTENDO_SDK_ROOT%\Tools\CommandLineTools\InitializeEdevWin.exe
	</code></pre>	
</details>


## Links and files

[NintendoSwitch-Terms.xlsm](https://prod-files-secure.s3.us-west-2.amazonaws.com/31274887-7daf-4c2f-b51f-9a29b90d9eb7/ae4dec49-d11a-4a3c-989c-a2c267257786/NintendoSwitch-Terms.xlsm)
