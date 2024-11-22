import tkinter as tk
from tkinter import filedialog, messagebox
import numpy as np
from scipy.signal import butter, iirpeak, lfilter, freqz
from pydub import AudioSegment
import os
import matplotlib.pyplot as plt
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

# Set ffmpeg for pydub
AudioSegment.converter = r"C:\\Program Files\\ffmpeg\\bin\\ffmpeg.exe"

class DigitalEqualizer:
    def __init__(self, root):
        self.root = root
        self.root.title("Digital Equalizer")
        self.input_file = None

        # GUI Elements
        self.init_gui()

    def init_gui(self):
        # File Selection
        tk.Button(self.root, text="Select MP3 File", command=self.select_file).pack()
        self.file_label = tk.Label(self.root, text="No file selected.")
        self.file_label.pack()

        # Filter Parameters
        self.filter_frames = []
        filter_frame = tk.Frame(self.root)
        filter_frame.pack(fill="x", padx=10, pady=10)

        for i in range(5):
            frame = tk.LabelFrame(filter_frame, text=f"Filter {i + 1}", padx=10, pady=10)
            frame.grid(row=0, column=i, padx=10, pady=5)

            # Type Selector
            tk.Label(frame, text="Type:").grid(row=0, column=0, sticky="w")
            type_var = tk.StringVar(value="HPF")
            tk.OptionMenu(frame, type_var, "HPF", "LPF", "Peak").grid(row=1, column=0)

            # Gain
            tk.Label(frame, text="Gain (dB):").grid(row=0, column=1)
            gain_scale = tk.Scale(frame, from_=-40, to=40, resolution=1, orient="vertical")
            gain_scale.grid(row=1, column=1)

            # Frequency
            tk.Label(frame, text="Frequency (Hz):").grid(row=0, column=2)
            freq_entry = tk.Entry(frame)
            freq_entry.grid(row=1, column=2)
            freq_entry.insert(0, "1000")

            # Q Factor
            tk.Label(frame, text="Q Factor:").grid(row=0, column=3)
            q_scale = tk.Scale(frame, from_=0.1, to=10, resolution=0.1, orient="vertical")
            q_scale.grid(row=1, column=3)

            self.filter_frames.append({
                "type_var": type_var,
                "gain_scale": gain_scale,
                "freq_entry": freq_entry,
                "q_scale": q_scale
            })

        # Equalizer Graph
        self.fig, self.ax = plt.subplots(figsize=(6, 4))
        self.ax.set_title("Equalizer")
        self.ax.set_xlabel("Frequency (Hz)")
        self.ax.set_ylabel("Gain (dB)")
        self.ax.set_xscale("log")
        self.ax.set_xlim(20, 20000)
        self.ax.set_ylim(-40, 40)
        self.ax.grid(which="both", linestyle="--", linewidth=0.5)
        self.canvas = FigureCanvasTkAgg(self.fig, master=self.root)
        self.canvas.get_tk_widget().pack(fill="both", padx=10, pady=10)

        # Buttons
        tk.Button(self.root, text="Update Graph", command=self.update_graph).pack()
        tk.Button(self.root, text="Process and Save", command=self.process_audio).pack()

    def select_file(self):
        self.input_file = filedialog.askopenfilename(filetypes=[("MP3 Files", "*.mp3")])
        if self.input_file:
            self.file_label.config(text=f"Selected: {self.input_file}")

    def update_graph(self):
        self.ax.clear()
        self.ax.set_title("Equalizer")
        self.ax.set_xlabel("Frequency (Hz)")
        self.ax.set_ylabel("Gain (dB)")
        self.ax.set_xscale("log")
        self.ax.set_xlim(20, 20000)
        self.ax.set_ylim(-40, 40)
        self.ax.grid(which="both", linestyle="--", linewidth=0.5)

        w = None
        combined_response = None

        for filter_params in self.filter_frames:
            filter_type = filter_params["type_var"].get()
            gain = filter_params["gain_scale"].get()
            freq = float(filter_params["freq_entry"].get())
            q = filter_params["q_scale"].get()

            if filter_type == "HPF":
                b, a = self.design_hpf(freq)
            elif filter_type == "LPF":
                b, a = self.design_lpf(freq)
            elif filter_type == "Peak":
                b, a = self.design_peak(gain, freq, q)

            w, h = freqz(b, a, fs=44100)

            # Convert response to dB
            h_dB = 20 * np.log10(abs(h))
            self.ax.plot(w, h_dB, label=f"{filter_type} @ {freq} Hz")

            # Sum responses
            if combined_response is None:
                combined_response = h
            else:
                combined_response *= h

        # Plot combined response
        if combined_response is not None:
            combined_response_dB = 20 * np.log10(abs(combined_response))
            self.ax.plot(w, combined_response_dB, color="black", linewidth=2, label="Combined Response")

        self.ax.legend()
        self.canvas.draw()

    def design_hpf(self, cutoff):
        nyquist = 44100 / 2
        normalized_cutoff = cutoff / nyquist
        b, a = butter(2, normalized_cutoff, btype='high')
        return b, a

    def design_lpf(self, cutoff):
        nyquist = 44100 / 2
        normalized_cutoff = cutoff / nyquist
        b, a = butter(2, normalized_cutoff, btype='low')
        return b, a

    def design_peak(self, gain, fc, q_factor):
        nyquist = 44100 / 2
        w0 = fc / nyquist
        g = 10 ** (gain / 20)
        b, a = iirpeak(w0, q_factor)
        b *= g
        return b, a

    def process_audio(self):
        if not self.input_file:
            messagebox.showerror("Error", "No input file selected.")
            return

        audio = AudioSegment.from_mp3(self.input_file)
        samples = np.array(audio.get_array_of_samples(), dtype=np.float32)
        samples /= np.max(np.abs(samples))  # Normalize to [-1, 1]

        for filter_params in self.filter_frames:
            filter_type = filter_params["type_var"].get()
            gain = filter_params["gain_scale"].get()
            freq = float(filter_params["freq_entry"].get())
            q = filter_params["q_scale"].get()

            if filter_type == "HPF":
                b, a = self.design_hpf(freq)
            elif filter_type == "LPF":
                b, a = self.design_lpf(freq)
            elif filter_type == "Peak":
                b, a = self.design_peak(gain, freq, q)

            samples = lfilter(b, a, samples)

        samples = (samples * (2 ** 15)).astype(np.int16)

        output_file = filedialog.asksaveasfilename(defaultextension=".mp3", filetypes=[("MP3 Files", "*.mp3")])
        if output_file:
            processed_audio = AudioSegment(
                samples.tobytes(),
                frame_rate=audio.frame_rate,
                sample_width=audio.sample_width,
                channels=audio.channels
            )
            processed_audio.export(output_file, format="mp3")
            messagebox.showinfo("Success", f"Processed file saved to {output_file}")

if __name__ == "__main__":
    root = tk.Tk()
    app = DigitalEqualizer(root)
    root.mainloop()
