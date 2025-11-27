# RMT-Finder Overview
'RMT-Finder' is a resource to streamline the determination of the Resting Motor Threshold (RMT) in Transcranial Magnetic Stimulation (TMS) studies. RMT-Finder is an automated procedure to determine the RMT in a shorter but reliable manner, reducing the workload for the experimenter by measuring Motor Evoked Potential (MEP) amplitudes, and automatically adjust stimulation intensity based on the responses observed.

# Resting Motor Threshold (RMT) definition

Using relative-frequency approaches the RMT is defined as the lowest stimulation intensity the produces MEPs with at least 50 µV peak-to-peak amplitude in 5 out of 10 consecutive trials ([Rossini et al. 2015](linkinghub.elsevier.com/retrieve/pii/S1388-2457(15)00071-1)). Therefore a 'valid' trial is an MEP with 50 µV amplitude or more, and an 'invalid' trial is an MEP with less than 50 µV amplitude. 

# RMT-Finder Algorithm

'RMT-Finder' uses a binary search algorithm. The user must specify a range of intensities, in % of Maximal Stimulator Output (%MSO), within which the RMT is plausible to be for a given individual. The algorithm will always start testing the midpoint of this defined search space. Every iteration (i.e., intensity tested as possible RMT), up to 10 trials will be collected. At a given intensity, there can be two outcomes:
- 5 valid trials are found. That intensity can be the RMT and therefore higher intensities cannot be the RMT. Therefore, the algorithm will discard all intensities above it and set this intensity -1% MSO as the upper limit of the new search space. In the next iteration, a new midpoint determined by this new search space will be tested.
- 6 invalid trials are found. That intensity cannot be the RMT and therefore lower intensities cannot be the RMT either. Therefore, the algorithm will discard all intensities below it and set this intensity +1% MSO as the lower limit of the new search space. In the next iteration, a new midpoint determined by this new search space will be tested.

The algorithm will repeat the process described above until the search space is exhausted (i.e. the lower and the upper limit are the same), and therefore at that point the lowest stimulation intensity tested will be the RMT.

'RMT-Finder' allows the user to specify a range of intensities based on the intensity at which the hostpot is found (if responses during hotspotting are around the desired 50 µV amplitude). For instance, a reasonable search space would be a hostpot intensity +/- 10% MSO, as the RMT is unlikely to be beyond that range. 'RMT-Finder' also allows the user to use the full range of the stimulation device as the search space (from 1% to 100% MSO).

# Technical specifications (software)

At present, RMT-Finder works as a script + sampling configuration developed in the [Signal programming language](https://ced.co.uk/products/sigovin) from [CED](https://ced.co.uk/). A version of Signal 6.04 or higher is required to run 'RMT-Finder'.

'RMT-Finder' requires the 'Magstim' module to be installed in Signal. This allows Signal to control stimulation parameters of different Magstim devices directly through a serial port (see Hardware).

The 'RMT-Finder' is composed of 2 main Signal files that you can download directly from this repository:
### 1. Sampling Configuration (.sgcx):
Contains the parameters.
There are two important tasks that must be done the first time you use 'RMT-Finder':
     1. Define the correct communication port for the serial port: Go to Sample > Sampling Configuration > States. In the bottom-left part you will see a button "Auxiliary devices". Click on it and a new menu will pop up. In that menu, select the appropriate COM port to which the stimulator is connected through the serial port.
     2. Define the desired Inter-trial interval (ITI): Go to Sample > Sampling Configuration > States > Configure Pulses.

Optional parameters: Trial length, sampling rate and other parameters can be also modified through the sampling configuration. Please read Signal's user manual for more information.

### 2. Script (.):
Contains the code, written in Signal's native programming language, to measure MEP peak-to-peak amplitudes and automatically adjust stimulation intensity accordingly.
Important parameters in the script:
1. The script assumes that:
      1. The EMG signal is recorded via numbered ADC channels (typically through a 1401 interface - see Hardware). By default, the first muscle will be ADC 1, and additional muscles should be recorded subsequently.
      2. Each trial is a sweep with length of 1 second of EMG recording, and the TMS pulse is delivered at 500ms (as per the sampling configuration).
      3. The envelope in which the MEP should be found is between 10ms and 50ms after the TMS pulse. It will measure the peak-to-peak amplitude of the EMG signal only in this envelope by default. The threshold for a valid trial is a peak-to-peak amplitude of the EMG signal of at least 50 µV.
      4. The individual is at rest. Background noise is checked as the Root Mean Square (RMS) of the EMG signal in an envelope of 100ms before the TMS pulse. It allows the user to optionally define a given value of RMS that is considered 'acceptable'. This will vary significantly across setups and participants. We recommend the user to set an appropriate value based on their setup specification.
      5. 

# Hardware

To allow communication between Signal and the Magstim device, you will need a serial port [ASK SPECIFICATIONS TO JULIEN]

# First use of the 'RMT-Finder'



# Subsequent uses of the 'RMT-Finder'
