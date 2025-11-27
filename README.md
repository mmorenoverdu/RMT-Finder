# RMT-Finder Overview
'RMT-Finder' is a resource to streamline the determination of the Resting Motor Threshold (RMT) in Transcranial Magnetic Stimulation (TMS) studies. RMT-Finder is an automated procedure to determine the RMT in a shorter but reliable manner, reducing the workload for the experimenter by measuring Motor Evoked Potential (MEP) amplitudes, and automatically adjust stimulation intensity based on the responses observed.

# Resting Motor Threshold (RMT) definition

Using relative-frequency approaches the RMT is defined as the lowest stimulation intensity the produces MEPs with at least 50 µV peak-to-peak amplitude in 5 out of 10 consecutive trials ([Rossini et al. 2015](linkinghub.elsevier.com/retrieve/pii/S1388-2457(15)00071-1)). Therefore a 'valid' trial is an MEP with 50 µV amplitude or more, and an 'invalid' trial is an MEP with less than 50 µV amplitude. 

# RMT-Finder Algorithm

'RMT-Finder' uses a binary search algorithm. The user must specify a range of intensities, in % of Maximal Stimulator Output (%MSO), within which the RMT is plausible to be for a given individual. The algorithm will always start testing the midpoint of this defined search space. Every iteration (i.e., intensity tested as possible RMT), up to 10 trials will be collected. At a given intensity, there can be two outcomes:
- 5 valid trials are found. That intensity can be the RMT and therefore higher intensities cannot be the RMT. Therefore, the algorithm will discard all intensities above it and set this intensity -1% MSO as the upper limit of the new search space. Therefore in the next iteration, a new midpoint determined by this new search space will be tested.
- 6 invalid trials are found. That intensity cannot be the RMT and therefore lower intensities cannot be the RMT either. Therefore, the algorithm will discard all intensities below it and set this intensity +1% MSO as the lower limit of the new search space. Therefore, in the next iteration, a new midpoint determined by this new search space will be tested.

The algorithm will repeat the process described above until the search space is exhausted (i.e. the lower and the upper limit are the same), and therefore at that point the lowest stimulation intensity tested will be the RMT.

'RMT-Finder' allows the user to specify a range of intensities based on the intensity at which the hostpot is found (if responses during hotspotting are around the desired 50 µV amplitude). For instance, a reasonable search space would be a hostpot intensity +/- 10% MSO, as the RMT is unlikely to be beyond that range. 'RMT-Finder' also allows the user to use the full range of the stimulation device as the search space (from 1% to 100% MSO).

# Technical specifications (software)

At present, RMT-Finder works as a script developed in the [Signal programming language](https://ced.co.uk/products/sigovin) from [Cambridge Electronic Designs](https://ced.co.uk/). A version of Signal 6.04 or higher is required to run the script.

'RMT-Finder' requires the 'Magstim' module to be installed in Signal. This allows Signal to control stimulation parameters of different Magstim devices directly through a serial port.

## Sampling Configuration in Signal


# Hardware
To allow communication between Signal and the Magstim device, you will 

# First use of the 'RMT-Finder'



# Subsequent uses of the 'RMT-Finder'
