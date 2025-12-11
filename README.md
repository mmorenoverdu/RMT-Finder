# RMT-Finder Overview

'RMT-Finder' is a resource developed to streamline the determination of the Resting Motor Threshold (RMT) in Transcranial Magnetic Stimulation (TMS) studies. It is an automated procedure to determine the RMT in a shorter but reliable manner, reducing the workload for the experimenter by measuring Motor Evoked Potential (MEP) amplitudes, and automatically adjusting stimulation intensity based on the responses observed.

With 'RMT-Finder', the experimenter only needs to focus on maintaining precise coil positioning during RMT determination, as everything else will be done automatically. The whole process will usually take less than 3 minutes and around 33-34 trials, with a maximum of 4 intensities tested in the fastest version of the procedure.

# Resting Motor Threshold (RMT) definition

According to relative-frequency approaches, the RMT is defined as the lowest stimulation intensity, in % of Maximal Stimulator Output (%MSO), that produces MEPs with at least 50 µV peak-to-peak amplitude in 5 out of 10 consecutive trials ([Rossini et al. 2015](linkinghub.elsevier.com/retrieve/pii/S1388-2457(15)00071-1)). Therefore:
- A 'valid' trial is an MEP with peak-to-peak amplitude ≥ 50 µV.
- An 'invalid' trial is an MEP with peak-to-peak amplitude < 50 µV. 

<img width="720" height="450" alt="TMS setup RMT-Finder" src="https://github.com/user-attachments/assets/606d5ca8-4df6-46b9-b294-db02c8ecc296" />


# RMT-Finder Algorithm

'RMT-Finder' uses a binary search algorithm. The algorithm requires the definition of a lower and an upper limit that define a search space. Every iteration, the algorithm tests the midpoint of the search space and, given the response, it reduces it by half each time.

In the 'RMT-Finder', a range of intensities (in % MSO) within which the RMT should be found, must be specified. The algorithm will always start testing the midpoint of this defined search space. Every iteration (i.e., intensity tested as candidate to be RMT), up to 10 trials will be collected. After testing a given intensity, there can be two outcomes:
- 5 valid trials are found. That intensity can be the RMT and therefore all higher intensities cannot be the RMT. The algorithm will discard all intensities above it and set this intensity -1% MSO as the upper limit of the new search space. In the next iteration, a new midpoint determined by this new search space will be tested.
- 6 invalid trials are found. That intensity cannot be the RMT and therefore all lower intensities cannot be the RMT either. The algorithm will discard all intensities below it and set this intensity +1% MSO as the lower limit of the new search space. In the next iteration, a new midpoint determined by this new search space will be tested.

<img width="720" height="450" alt="Binary search algorithm" src="https://github.com/user-attachments/assets/09e88ae5-96c0-45e3-83da-8bd6f99796ed" />

The algorithm will repeat the process described above until the search space is exhausted (i.e. the lower and the upper limit are the same), and therefore at that point the lowest stimulation intensity meeting the criterion will be the RMT. Note that these two outcomes do not technically require 10 trials on every iteration: the algorithm will proceed to the next step as soon as 5 valid or 6 invalid trials are found, therefore speeding up the process.

Users can specify the initial search space in two ways:

**1. Relative Search Space:** Specify a range of intensities relative to the intensity at which the hostpot is found. If responses during hotspotting are around the desired 50 µV threshold amplitude, it is reasonable to assume that the RMT will be close to the hotspot intensity. By default, 'RMT-Finder' will use a range determined as hostpot intensity ± 10% MSO, as the RMT is unlikely to be beyond that range if the coil position is maintained. The user can easily modify this in the provided script.

**2. Absolute Search Space:** Use the full range of the stimulation device as the search space (from 1% to 100% MSO), or an arbitrary wide range (e.g. 20% to 90% MSO). This will be slower as the algorithm will test intensities implausibly lower/higher for a given individual (e.g. if the hotspot is found with intensities around 40% MSO, it is unlikely that the RMT will be 70% MSO). However, the Absolute and the Relative methods will converge to the same RMT within ± 2% MSO according to our previous data ([Boidequin et al. 2026]). We do not recommend the Absolute method because it is slower and high intensities might cause unnecesary discomfort for the person. Therefore, we highly recommend the Relative method.

# Technical specifications (software)

At present, RMT-Finder works as a script + sampling configuration developed in the [Signal](https://ced.co.uk/products/sigovin) programming language from [CED](https://ced.co.uk/). A version of Signal 6.04 or higher is required to run 'RMT-Finder'.

'RMT-Finder' has been tested using [Magstim](https://www.magstim.com/) devices ([200](https://www.magstim.com/product/magstim-m200%c2%b2/) and [BiStim](https://www.magstim.com/product/magstim-bistim2/), but could also be used for [Rapid](https://www.magstim.com/product/rapid%c2%b2/)). It thus requires the 'Magstim' extension module to be installed in Signal. This allows Signal to control stimulation parameters (e.g. stimulator output) of the stimulation device directly through a serial port (see Hardware). Please note that Signal may be compatible with stimulation devices from [Magventure](https://magventure.com/) and other companies (see [this document]()).

'RMT-Finder' is composed of 2 main Signal files that you can download directly from this repository:
### 1. Sampling Configuration (.sgcx):
Contains important sampling parameters such as trial length, inter-trial interval, sampling frequency, input and output channels, etc.
There are two important settings in this file that must be specified the first time you use 'RMT-Finder':
1. Define the correct communication port for the serial port: Go to Sample > Sampling Configuration > States. In the bottom-right corner you will see an "Auxiliary" button. Click on it and a new menu will pop up to select which type of device you want to configure. Once you have selected it, a new menu will appear. In this menu, select the appropriate COM port of your computer, to which the stimulator is connected through the serial port.
2. Define the desired Inter-trial interval (ITI): Go to Sample > Sampling Configuration > States > Configure Pulses. In the pop-up menu, the Interval (in seconds) and its variation (in % of time) can be set. By default, 'RMT-Finder' will use a fixed and short ITI of 4 seconds. This gives sufficient time to the stimulator device to recharge between pulses. Note that if you want to change the ITI [CAN THIS BE DONE AUTOMATICALLY IN THE SCRIPT???]

Input/output channels configuration:
1. Input channels: These channels read EMG signals directly from a 1401 interface ([Power](https://ced.co.uk/products/pow3in)/[Micro](https://ced.co.uk/products/mic4in)), also by CED. The 1401 coordinates inputs and outputs in a timely manner. Typically, the 1401 will be receiving inputs from the EMG amplifier through BNC cables. The input channels are Analogue to Digital (ADC) channels and are numbered. Go to Sample > Sample Configuration. In the General tab, you can modify the number of ADC channels that are available (4 by default, Signal starts at number #0). In the Port Setup tab, you can modify each channel's configuration (e.g. labels).
2. Output channels: These channels send triggers directly from the 1401 interface to the stimulator device. This can be done through the Digital to Analogue (DAC) channels or through the Digital Outputs channels, both from the 1401. 'RMT-Finder' is set to use the Digital Output channels by default. Go to Sample > Sample Configuration > Outputs. You can modify the number of DAC/Digital Output channels available (by default, 'RMT-Finder' will send triggers through Digital Outputs #0 and #1).

Optional parameters: Sampling rate and other parameters can be also modified through the sampling configuration. Please read Signal's user manual for more information.

*Reminder*: Signal does not automatically save changes in the Sampling Configuration. Make sure to always go to File > Save Configuration As, to save your modifications!

### 2. Script (.sgs):
Contains the code, written in Signal's native programming language, to measure MEP peak-to-peak amplitudes and automatically adjust stimulation intensity accordingly. To collect data, the script will load the sampling configuration template and will create a toolbar with a 'RMT-Finder START' and 'PAUSE'/'QUIT' buttons.

Important default parameters in the script. The script *assumes* that:
1. The EMG signal is recorded via numbered ADC channels (typically through a 1401 interface - see Hardware). By default, the first muscle will be ADC #0, and additional muscles should be recorded subsequently.
2. Each trial is a sweep with length of 1 second of EMG recording, and the TMS pulse is delivered in the middle of the trial, at 500ms (as per the sampling configuration).
3. The EMG signal is already online amplified and band-pass filtered. This is an important factor to consider when measuring MEPs. Make sure your EMG amplifier has sensible configurations.
4. The envelope in which the MEP should be found is between 10ms and 50ms after the TMS pulse (for upper limb muscles, MEP latencies are around 20-25ms, for lower limb muscles, around 30-35ms). 'RMT-Finder' will measure the peak-to-peak amplitude of the EMG signal only in this envelope by default, but the envelope can be easily changed in the script. The threshold for a valid trial is a peak-to-peak amplitude of the EMG signal of at least 50 µV, which can be also eaisly changed in the script.
5. The individual is at rest. Background noise is checked as the Root Mean Square (RMS) of the EMG signal in an envelope of 100ms before the TMS pulse.The script allows the user to optionally define a given value of RMS that is considered 'acceptable' (and therefore if higher values are found, the trial will not be considered for RMT determination). It also allows to change the desired envelope. Because absolute RMS values will vary significantly across setups and participants and can be impacted by the sampling frequency, bandpass filters, etc., we recommend the user to set an appropriate value based on their setup specification.

Adjustable parameters in the script and their defaults:
1. Magstim device: BiStim (default) and 200 stimulators.
2. Number of muscles: From 1 (default) up to 4.
3. Threshold for MEP amplitude: 50 µV.
4. MEP envelope: 10-50ms after pulse.
5. Threshold (RMS) for background noise: 50 µV.
6. Background noise envelope: 1-100ms before pulse.
7. Relative search space limits (method 'hotspot ± range'): -10% to +10% MSO.
8. Absolute search space limits: 20% to 90% MSO.

# Hardware

Hardare requirements include:
1. A computer running Signal, with drivers installed for the 1401 interface and the EMG amplifier/software.
2. A serial port cable with the pins set up according to the device's user manual (e.g. [BiStim]())

To allow communication between Signal and the Magstim device, you will need a serial port [ASK SPECIFICATIONS TO JULIEN]

# Usage of the 'RMT-Finder'

The workflow to use the 'RMT-Finder' is very simple:
1. Hardware settings:
    1. Connect the serial port from the computer running Signal to the stimulator device. Remember it must be the correct COM port.
    2. Connect the Trigger In BNC cable from the back of the stimulator device to the Digitial Output 0 in the 1401 interface.
    3. Connect the EMG amplifier to ADC channel 0 in the 1401 interface (if only 1 muscle is recorded).
2. Open Signal.
3. Go to File > Load Configuration. Open the corresponding 'RMT-Finder' sampling configuration.
4. *Optional*: Go to the Magstim module and click on 'Test' to confirm that communication with the device is correct.
5. **Click 'OK'. DO NOT click on 'Run now'**. This will open the data collection tab and will make the script unable to work.
6. Go to Script > Run script > Load and run. Select the corresponding 'RMT-Finder' script.
7. *Optional*: make changes in the script as necessary. The script is well-documented and self-explanatory.
8. Click on the 'Run script' button (upper right corner). This will open a pop-up menu where you can select the method to find the RMT:
    1. If you input a hotspot intensity (must be >0% MSO), this will set a Relative search space, whereby the first intensity tested will be the hotspot intensity and the range will be ± 10% MSO around it, by default.
    2. If you input a value of 0, this will set an Absolute search space defined by the script, with 20% to 90% MSO as default values.
9. This will open a data collection sheet directly and a toolbar with the 'RMT-Finder' buttons.
10. **DO NOT use Signal's native 'START' button** (in the upper left corner). Instead, use the **'START RMT-Finder' button** (in the upper right corner that is in the toolbar).

Once you click START, the script will:
1. Open a data collection file.
2. Open a log text file: MEP amplitudes, RMS values, number of trials and intensities tested will be printed online here.
3. First deliver a 'dummy' trial at 1% MSO to confirm that communication and settings are correct.
4. Adjust the stimulator's intensity to the midpoint of the defined search space.
5. Start delivering trials in the search of the RMT!
6. Once the algorithm has found the RMT, an auditory message will indicate that data collection is complete.
7. At the bottom of the log text document: A quick summary including the RMT value, the number of pulses, time from first to last pulse and number of intensities will be printed.
