# DigitalEQ
Digital Signal Processing Project where we implement a Digital Parametric Equaliser with a GUI using Python.

### **Report: Digital Parametric Equalizer Implementation**

#### **Introduction**
A parametric equalizer is a tool commonly used in audio signal processing to adjust the frequency response of an audio signal. Unlike graphic equalizers that control fixed frequency bands, parametric equalizers allow users to modify specific parameters of each band, including center frequency, bandwidth (or quality factor, \( Q \)), and gain. This flexibility makes them ideal for applications like audio enhancement, noise removal, and creative sound design.

This report presents a digital parametric equalizer implemented in Python using a Graphical User Interface (GUI). The system is capable of applying multiple filters—high-pass (HPF), low-pass (LPF), and peak filters—to modify the spectral content of an input MP3 file.

---

#### **Theory of Parametric Equalizers**
A parametric equalizer applies filters to audio signals based on the desired frequency domain manipulations. Each filter type operates differently:

1. **High-Pass Filter (HPF)**:
   - Removes frequencies below a specified cutoff frequency.
   - Designed as a 2nd-order Butterworth filter for flat frequency response in the passband.
   - Defined by its cutoff frequency.

2. **Low-Pass Filter (LPF)**:
   - Removes frequencies above a specified cutoff frequency.
   - Designed similarly to the HPF as a 2nd-order Butterworth filter.
   - Defined by its cutoff frequency.

3. **Peak Filter**:
   - Boosts or attenuates frequencies around a center frequency.
   - Defined by:
     - **Gain**: The magnitude of amplification or attenuation.
     - **Center Frequency**: The target frequency for adjustment.
     - **Q Factor**: Controls the bandwidth of the affected frequencies.

   The peak filter is implemented using a digital Infinite Impulse Response (IIR) filter. The transfer function of a peak filter is given by:
   \[
   H(z) = \frac{b_0 + b_1z^{-1} + b_2z^{-2}}{1 + a_1z^{-1} + a_2z^{-2}}
   \]
   where coefficients \( b_0, b_1, b_2, a_1, a_2 \) depend on the desired gain, center frequency, and Q factor.

These filters are cascaded to achieve the final combined frequency response.

---

#### **Features of the GUI**
The graphical user interface (GUI) simplifies interaction with the equalizer. Key features include:

1. **Filter Configuration**:
   - Users can configure up to five filters, each with:
     - **Filter Type Selector**: Choose HPF, LPF, or Peak.
     - **Gain Control**: Adjust the filter gain (in dB) using a vertical slider.
     - **Frequency Input**: Enter the cutoff or center frequency in Hz.
     - **Q Factor Control**: Adjust the quality factor for peak filters.

2. **Frequency Response Visualization**:
   - Displays individual filter responses.
   - Plots a **combined response curve** in black, showing the cumulative effect of all filters.

3. **Audio Processing**:
   - Allows selection of an input MP3 file.
   - Processes the audio with the selected filters and saves the result as a new MP3 file.

4. **Interactive Graph**:
   - Real-time updates of the frequency response graph based on user inputs.

---

#### **Process to Run the Code**
To successfully run the code and utilize the digital equalizer, follow these steps:

1. **Install Required Libraries**:
   Ensure the following Python libraries are installed:
   - `numpy`
   - `scipy`
   - `pydub`
   - `matplotlib`
   - `tkinter`

   You can install missing packages using:
   ```bash
   pip install numpy scipy pydub matplotlib
   ```

2. **Set Up FFmpeg**:
   - Download and install FFmpeg from [FFmpeg official website](https://ffmpeg.org/).
   - Update the path to `ffmpeg.exe` in the code:
     ```python
     AudioSegment.converter = r"C:\\Program Files\\ffmpeg\\bin\\ffmpeg.exe"
     ```

3. **Run the Program**:
   - Save the code as a `.py` file (e.g., `equalizer.py`).
   - Execute it using:
     ```bash
     python equalizer.py
     ```

4. **Select an MP3 File**:
   - Click **"Select MP3 File"** and choose an input MP3 file.

5. **Configure Filters**:
   - Select the filter type for each band.
   - Adjust the gain, frequency, and Q factor using sliders or text entries.

6. **Visualize the Frequency Response**:
   - Click **"Update Graph"** to see the filter responses and the combined response.

7. **Process Audio**:
   - Click **"Process and Save"** to generate a new MP3 file with the applied filters.

---

#### **Conclusion**
The digital parametric equalizer demonstrates the practical implementation of advanced filtering techniques in digital signal processing. The interactive GUI facilitates easy experimentation with various filter parameters, providing both visual and auditory feedback. The addition of real-time graphing of the combined response ensures users understand the cumulative effect of all filters.

Future enhancements could include:
- Real-time playback of processed audio.
- Support for additional filter types.
- More flexible frequency response customization.

This project provides hands-on experience with digital filtering concepts, signal flow in audio processing, and GUI development, making it a valuable learning tool for DSP applications.
