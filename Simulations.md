# PYTHON Code 
``` Python
import numpy as np
import matplotlib.pyplot as plt

c = 3*10^8

def radar_wavelength(frequency):
    return c / frequency

def rcs_sphere(size, wavelength, reflectivity, aspect_angle):
    angle_rad = np.radians(aspect_angle)
    rcs = (np.pi**2 * size**2 * reflectivity) / (wavelength**2) * np.cos(angle_rad)**2
    return max(rcs, 0)  #RCS can't be negative

frequencies = np.array([1e9, 5e9, 10e9]) # Frequency
sizes = np.array([1])  #1 m
reflectivities = np.array([0.5])  # Reflectivity 
aspect_angles = np.arange(0, 91, 10)  # Aspect angle

def simulate_rcs(frequencies, aspect_angles, sizes, reflectivities):
    results_with_rcs = {}
    results_without_rcs = {}
    for freq in frequencies:
        wavelength = radar_wavelength(freq)
        for size in sizes:
            for reflectivity in reflectivities:
                rcs_with = [rcs_sphere(size, wavelength, reflectivity, angle) for angle in aspect_angles]
                results_with_rcs[(freq, size, reflectivity)] = rcs_with
                rcs_without = [0] * len(aspect_angles)  # No RCS
                results_without_rcs[(freq, size, reflectivity)] = rcs_without
    return results_with_rcs, results_without_rcs

rcs_values_with, rcs_values_without = simulate_rcs(frequencies, aspect_angles, sizes, reflectivities)

plt.figure(figsize=(14, 10))

plt.subplot(2, 2, 1)
for freq in frequencies:
    rcs_with_freq = rcs_values_with[(freq, sizes[0], reflectivities[0])]
    rcs_without_freq = rcs_values_without[(freq, sizes[0], reflectivities[0])]
    plt.plot(aspect_angles, rcs_with_freq, label=f'{freq/1e9} GHz (w RCS)')
    plt.plot(aspect_angles, rcs_without_freq, '--', label=f'{freq/1e9} GHz (Without RCS)')
plt.xlabel('Aspect Angle')
plt.ylabel('RCS')
plt.title('RCS vs Aspect Angle for Different Frequencies')
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 2)
freqs = np.linspace(1e9, 10e9, 100)  # Frequency range
rcs_with_freq_range = []
rcs_without_freq_range = []
for freq in freqs:
    wavelength = radar_wavelength(freq)
    max_rcs_with = max(rcs_sphere(sizes[0], wavelength, reflectivities[0], angle) for angle in aspect_angles)
    max_rcs_without = 0  # No RCS
    rcs_with_freq_range.append(max_rcs_with)
    rcs_without_freq_range.append(max_rcs_without)
plt.plot(freqs / 1e9, rcs_with_freq_range, label='With RCS')
plt.plot(freqs / 1e9, rcs_without_freq_range, '--', label='Without RCS')
plt.xlabel('Frequency (GHz)')
plt.ylabel('Max RCS (m²)')
plt.title('Max RCS vs Frequency for Given Size and Reflectivity')
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 3)
wavelengths = radar_wavelength(frequencies)
rcs_with_wavelength = []
rcs_without_wavelength = []
for wavelength in wavelengths:
    max_rcs_with = max(rcs_sphere(sizes[0], wavelength, reflectivities[0], angle) for angle in aspect_angles)
    max_rcs_without = 0  # No RCS
    rcs_with_wavelength.append(max_rcs_with)
    rcs_without_wavelength.append(max_rcs_without)
plt.plot(wavelengths, rcs_with_wavelength, 'o-', label='With RCS')
plt.plot(wavelengths, rcs_without_wavelength, '--', label='Without RCS')
plt.xlabel('Wavelength (m)')
plt.ylabel('Max RCS (m²)')
plt.title('Max RCS vs Wavelength')
plt.legend()
plt.grid(True)

plt.subplot(2, 2, 4)
specific_freq = 5e9  # 5 GHz
wavelength = radar_wavelength(specific_freq)
rcs_with_specific_freq = [rcs_sphere(sizes[0], wavelength, reflectivities[0], angle) for angle in aspect_angles]
rcs_without_specific_freq = [0] * len(aspect_angles)  # No RCS
plt.plot(aspect_angles, rcs_with_specific_freq, label='With RCS')
plt.plot(aspect_angles, rcs_without_specific_freq, '--', label='Without RCS')
plt.xlabel('Aspect Angle (degrees)')
plt.ylabel('RCS (m²)')
plt.title('RCS vs Aspect Angle for 5 GHz')
plt.legend()
plt.grid(True)

plt.tight_layout()
plt.show()
```
# SIMULATION RESULTS
![Figure_1](https://github.com/user-attachments/assets/69e4019e-78b2-474a-baa0-e02e490b0bfb)



