# TLIST Processor

Software is designed to process two-dimensional Doppler energy spectra. The background fitting and subtraction mechanism is implemented. Ridge profile along every energy axis is fitted with a step function that accuonts on higher energetic Compton scattered photons (constant background along the energy axis) and Compton scattering of annihilation photons outside of the detector volume (~constant contribution in the range < 511 keV). An exponential contribution in the range < 511 keV is found due to incomplete charge collection in the detector. Another exponential contribution in the region > 511 keV occurs due to the pile-up effect.

<img src="http://physics.bgsu.edu/selimlab/wp-content/uploads/2016/12/tlist-processor-screenshot.png" alt="SW Calculator desktop application" style="width: 100%;"/>

After the background subtraction software transforms two-dimensional histogram into a one-dimensional by introducing a new variable E1-E2. Check our [research group website SelimLab](http://physics.bgsu.edu/~faselim/).

## Setup instructions

Application is written in C++ with CERN ROOT libraries and GUI. In order to run the app first we need to install ROOT libraries. Unfortunately it is impossible to do static link your applications object files with the ROOT libraries as well as build their static versions. Moreover, sometimes source headers should be available at runtime.

Currently it is possible to run application either on macOS or Linux. I will try to compile the windows binary whenever the native Windows ROOT 6 build will be released.

Below find the instructions for macOS setup. Linux users should be able to

### Installing xCode

6. Download xCode here [https://developer.apple.com/xcode/downloads/](https://developer.apple.com/xcode/downloads/).Note. I've tested new xCode 9 on macOS Sierra on Nov 4, 2017 and it turned out that it throws some compilation errors. As a temporary workaround I recommend downloading older xCode versions here [https://developer.apple.com/download/more/](https://developer.apple.com/download/more/). I used 8.3.3

7. Accept xCode license. Open Terminal and enter:

```bash
sudo xcodebuild -license
```

8. Install xCode command-line tools from Terminal

```bash
xcode-select --install
```

### Installing ROOT

#### Method 1. Binary distribution
Download and install the correct package (.dmg) for your macOS version here [https://root.cern.ch/downloading-root](https://root.cern.ch/downloading-root)

#### Method 2. Compile from source

Sometimes the binary distribution on the ROOT website might not yet be available if you are running a recent release of macOS. In this case you have to build ROOT from source.

You will need to install CMake first. Get it here [https://cmake.org/download/](https://cmake.org/download/). Install a .dmg and enable console use by modifying PATH variable. Do `nano ~/.bash_profile` and add following:

```bash
PATH=$PATH:/Applications/CMake.app/Contents/bin
export PATH
```

Current software requires ROOT to be built with FFTW libraries. First, [http://www.fftw.org/download.html](download) and unpack FFTW sources to your computer. Navigate to the correspondent folder and run:

```bash
./configure
make
sudo make install
```
Next download ROOT sources that can be built on your system. Tip: if you are running macOS High Sierra 10.13 you can go only with ROOT 10.11.02 and newer. I've succeeded building it with xCode 9. Unpack your ROOT sources and do:

```bash
mkdir build
cd build
cmake -Dfftw3=ON -DFFTW_LIBRARY=/usr/local/lib/libfftw3.a -DFFTW_INCLUDE_DIR=/usr/local/include -Droofit=ON /path/to/your/root/source/directory
```
Here we turn on support for `fftw3` and `roofit`. Double check that your paths to `libfftw3.a` and `libfftw3.h` are correct. Next,

```bash
make -j8
```
Here `-j8` specifies number of your CPU cores.

#### Setup ROOT environment variables
After you built the ROOT open Terminal, set **ROOTSYS** variable and add ROOT to **$PATH**. In order to be able to dynamically link your executables with ROOT libraries set **DYLD_LIBRARY_PATH**. Execute `nano ~/.bash_profile` and add following lines:

```bash
ROOTSYS=/Users/petrstepanov/root_v6.11.02
PATH=$PATH:$ROOTSYS/bin
DYLD_LIBRARY_PATH=$DYLD_LIBRARY_PATH:$ROOTSYS/lib

export ROOTSYS
export PATH
export DYLD_LIBRARY_PATH
```

Instructions for other platforms can be found here [https://root.cern.ch/root/EnvVars.html](https://root.cern.ch/root/EnvVars.html)

Now you should be able to run ROOT by typing `root` in Terminal.

### Building the ROOT application
Optional. Some GUI ROOT apps require X server. Not sure if this program really needs it but anyway. Download and install xQuartz from [https://www.xquartz.org](https://www.xquartz.org)

Download and unpack source package from this page. Green button `Clone or download` on the top right of the page.

Open Terminal, navigate into the unpacked folder in Terminal and type `make`. Your executable will show up under `dist/` folder.

Cern ROOT apps sometimes require sources at runtime. Declare `ROOT_INCLUDE_PATH` environment variable containing the path to the software's unpacked folder. Again open `nano ~/.bash_profile` and add following line:

```bash
export ROOT_INCLUDE_PATH=/path/to/your/application/folder:$ROOT_INCLUDE_PATH
```

### Creating the launcher
Open `Automator` macOS application. Select `Application` and click `Choose` button on the bottom right. In the list of action locate and drag `Run AppleScript` to the right actions panel. Enter following text into the AppleScript textarea

```bash
on run {input, parameters}
  tell application "Terminal"
    activate
    do script with command "cd /path/to/executable/dist/folder/;./tlist-processor"
  end tell
end run
```

Save the script and give it a desired name `SW Calculator`? Next in order to change the application icon locate your app in Finder under `/Applications/` folder, right click and select `Show Package Contents`. Go to `Contents/Resources`. Replace `AutomatorApplet.icns` with an `.icns` file from the `resources` folder in GitHub repo but keep the name `AutomatorApplet.icns`. Voila now you can see a neat icon in Spotlight. Dang cool.

---

Shoot me an email with feedback or questions: [petrs@bgsu.edu](mailto:petrs@bgsu.edu)
