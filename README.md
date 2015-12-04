# Tutorial on setting up GenOpt for EnergyPlus V8.4.0

I created this tutorial after seeing several questions on Unmet Hours where users were having trouble setting up GenOpt to run with newer that 7.0.0 versions of EnergyPlus. ([1](https://unmethours.com/question/4584/how-to-run-genopt/) and [2](https://unmethours.com/question/13100/error-trying-to-run-genopt/))

### 1. Install GenOpt.

Download GenOpt, currently version 3.1.0 from the [GenOpt website](http://simulationresearch.lbl.gov/GO/download.html).

Install to your local drive. If you are on Windows and you get an error saying "Cannot write to path" with the default path of `C:\Program Files (x86)\genopt`, open an elevated command prompt (type in "cmd" in the search bar and run as administrator). Then `cd` to the folder where you have the `genopt-install-3-1-0.jar` and type in `java -jar genopt-install-3-1-0.jar` and it'll do the trick.

### 2. Find an example to run

We're going to take in the example GPSHookeJeeves of 7.0.0.

GenOpt comes with examples up to E v7.0.0. Go to your genopt installation folder, and find the GSPHookeJeeves example for energyplus. The full path for me is `C:\Program Files (x86)\genopt\example\energyplus\7_0_0\GPSHookeJeeves`

Copy all the content of the folder and go put it somewhere else, like in your Documents folder or whatever.

### 3. Update IDF files to latest version.

Using the IDFVersionUpdater, update the **TWO** idf files in there to E v8.4.0.

By default any install of E+ comes with a version updater that handles the last transition (v8.3 to 8.4 for example). The IDFVersionUpdater is a preprocessor, so you'll find it in `C:\EnergyPlusV8-4-0\PreProcess\IDFVersionUpdater\IDFVersionUpdater.exe`

You'll need more than that to go from v7.0.0 to v8.4.0. Download the [MultipleIDFVersionTranslater](http://energyplus.helpserve.com/knowledgebase/article/View/86/46/windows---programs-for-converting-older-version-idf-files-to-current-or-intermediate-versions) from the helpdesk. Either take only the files you need or just extract it in full and run the IDFVersionUpdater.exe. If oyu only want some of these files, you can copy them and paste them in your E+ IDFVersionUpdater folder (default path is above) so that you end up with these files:

![List of files needed for IDFVersionUpdater](Images/IDFVersionUpdater.png?raw=true "Files needed for IDF Version Updater")


**Do the conversion.**

### 4. Configure!

If you try to run GenOpt directly, chances are you'll get an error message such as:

     Cannot find optimization configuration file: 'C:\Users\cfg\EnergyPlus-7-0-0-Win7.cfg'.

The path will be different in your case, depending on where you have created your test folder (it's a relative path, we'll see that).

So we need a .cfg file apparently. To run a GenOpt project, you need to supply an `*.ini` file, in your case `optWin7.ini`. 

This has the path to the two `*.idf` files, the path to the configuration file `*.cfg`, and the optimization file `command.txt`. There's also an optional call parameter for the weather file that defaults to the Chigaco OHare file.

      CallParameter { // optional section
        // The weather file without extension
        Suffix = USA_IL_Chicago-OHare.Intl.AP.725300_TMY3;
      }

First, go find the Chicago O hare weather file and paste it in your test folder (or put another one and change the CallParameter.

### 5. Create a `cfg` file

You can either create one in your test folder or in the genopt installation folder.

#### a. Create the cfg in your test folder.

Go to `C:\Program Files (x86)\genopt\cfg` to get the `EnergyPlus-7-0-0-Win7.cfg`, and copy and paste it in your test folder. Rename to `EnergyPlus-8-4-0-Win7.cfg` and open it up in your favorite text editor. Review and modify the `SimulationStart` command to point to your actual E+ installation folder.

**Before:**

    SimulationStart
    {
        // The command line below calls RunEPlus.bat.
        Command = "cmd /C \"\"C:\\Program Files \(x86\)\\EnergyPlusV8-4-0\\RunEPlus.bat\" \"%Simulation.Files.Input.File1%\" \"%Simulation.CallParameter.Suffix%\"\"";
        WriteInputFileExtension = false;
    }

My EnergyPlus folder is not at `C:\Program Files (x86)\EnergyPlusV8-4-0` but at `C:\\EnergyPlusV8-4-0`. Therefore I'll change the cfg file to be:

**After:**


    SimulationStart
    {
        // The command line below calls RunEPlus.bat.
        Command = "cmd /C \"\"C:\\EnergyPlusV8-4-0\\RunEPlus.bat\" \"%Simulation.Files.Input.File1%\" \"%Simulation.CallParameter.Suffix%\"\"";
        WriteInputFileExtension = false;
    }

In `opWin7.ini` I need to change the path to the configuration file to point this `*.cfg`'s location.

**Before:** (the `..` means "go up one folder" relative to this one)

    Configuration {
      File1 = "..\\..\\..\\..\\cfg\\EnergyPlus-7-0-0-Win7.cfg";
    }

**After:**

    Configuration {
      File1 = "EnergyPlus-8-4-0-Win7.cfg";
    }

#### b. Create the cfg in your genopt install folder.

Just do the same, and change the path in your optWin7.ini to point to `C:\Program Files (x86)\genopt\cfg\EnergyPlus-8-4-0-Win7.cfg`

### 6. Check that you have everything you need, and run

![All the files you need](Images/SetupAndRun.png?raw=true "Setup And Run")
