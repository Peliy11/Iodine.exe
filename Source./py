import win32gui
import win32con
import win32api
import win32ui
import ctypes
import math
import random
import time
import colorsys
import winsound
import sys

# --- PRE-RUN WARNINGS ---
def show_warnings():
    # First Warning: General Info
    warn1 = win32api.MessageBox(0, 
        "WARNING: This program is a GDI Simulation.\n\n"
        "It DOES NOT affect your machine permanently and DOES NOT destroy any files.\n"
        "It is purely visual. Do you wish to proceed?", 
        "GDI Warning", 
        win32con.MB_YESNO | win32con.MB_ICONWARNING)
    
    if warn1 != win32con.IDYES:
        sys.exit()

    # Second Warning: Liability
    warn2 = win32api.MessageBox(0, 
        "FINAL NOTICE:\n\n"
        "Peliy11 is not at fault for any hardware stress or seizure-related issues.\n"
        "DO NOT use this for harm or to prank people without their consent.\n"
        "Are you sure you want to run the simulation?", 
        "Legal Disclaimer", 
        win32con.MB_YESNO | win32con.MB_ICONERROR)

    if warn2 != win32con.IDYES:
        sys.exit()

# Run the warnings before doing anything else
show_warnings()

# --- INITIALIZATION ---
user32 = ctypes.windll.user32
user32.SetProcessDPIAware()
sw, sh = user32.GetSystemMetrics(0), user32.GetSystemMetrics(1)
gdi32 = ctypes.windll.gdi32

# Setup BITMAPINFO for XOR Phase
class BITMAPINFOHEADER(ctypes.Structure):
    _fields_ = [
        ("biSize", ctypes.c_uint32), ("biWidth", ctypes.c_int32), ("biHeight", ctypes.c_int32),
        ("biPlanes", ctypes.c_uint16), ("biBitCount", ctypes.c_uint16), ("biCompression", ctypes.c_uint32),
        ("biSizeImage", ctypes.c_uint32), ("biXPelsPerMeter", ctypes.c_int32), ("biYPelsPerMeter", ctypes.c_int32),
        ("biClrUsed", ctypes.c_uint32), ("biClrImportant", ctypes.c_uint32),
    ]
class BITMAPINFO(ctypes.Structure):
    _fields_ = [("bmiHeader", BITMAPINFOHEADER), ("bmiColors", ctypes.c_uint32 * 1)]

bmi = BITMAPINFO()
bmi.bmiHeader.biSize = ctypes.sizeof(BITMAPINFOHEADER)
bmi.bmiHeader.biWidth = sw
bmi.bmiHeader.biHeight = -sh
bmi.bmiHeader.biPlanes = 1
bmi.bmiHeader.biBitCount = 32
bmi.bmiHeader.biCompression = win32con.BI_RGB
pixel_ptr = ctypes.c_void_p()

hdc_screen_init = win32gui.GetDC(0)
hdc_screen_ui = win32ui.CreateDCFromHandle(hdc_screen_init)
hdc_mem = hdc_screen_ui.CreateCompatibleDC()
dib = gdi32.CreateDIBSection(hdc_screen_init, ctypes.byref(bmi), win32con.DIB_RGB_COLORS, ctypes.byref(pixel_ptr), None, 0)
bitmap = win32ui.CreateBitmapFromHandle(dib)
hdc_mem.SelectObject(bitmap)
pixel_array = ctypes.cast(pixel_ptr, ctypes.POINTER(ctypes.c_uint32))

font_data = {'name': 'Arial', 'height': 160, 'weight': 900}
massive_font = win32ui.CreateFont(font_data)

def draw_gradient_triangle(hdc, sw, sh):
    for _ in range(5):
        vertices = (
            {"x": random.randint(0, sw), "y": random.randint(0, sh), "Red": random.randint(0, 0xFF00), "Green": 0, "Blue": 0, "Alpha": 0},
            {"x": random.randint(0, sw), "y": random.randint(0, sh), "Red": 0, "Green": random.randint(0, 0xFF00), "Blue": 0, "Alpha": 0},
            {"x": random.randint(0, sw), "y": random.randint(0, sh), "Red": 0, "Green": 0, "Blue": random.randint(0, 0xFF00), "Alpha": 0},
        )
        win32gui.GradientFill(hdc, vertices, ((0, 1, 2),), win32con.GRADIENT_FILL_TRIANGLE)

# --- VARS ---
start_time = time.time()
count = 0
hue = 0
move_offset = 0  
sine_angle = 0
scaling_factor = 10 

try:
    try:
        winsound.PlaySound("virus_final_fixed.wav", winsound.SND_FILENAME | winsound.SND_ASYNC | winsound.SND_LOOP)
    except:
        pass

    while True:
        elapsed = time.time() - start_time
        hdc_handle = win32gui.GetDC(0)
        current_dc_ui = win32ui.CreateDCFromHandle(hdc_handle)

        if elapsed <= 20:
            pulse = int(math.sin(count) * 500)
            win32gui.StretchBlt(hdc_handle, -pulse, -pulse, sw + (pulse * 2), sh + (pulse * 2), hdc_handle, 0, 0, sw, sh, win32con.SRCCOPY)
            count += 0.1
            if elapsed > 5:
                rgb = colorsys.hsv_to_rgb(hue % 1.0, 1.0, 1.0)
                brush = win32gui.CreateSolidBrush(win32api.RGB(int(rgb[0]*255), int(rgb[1]*255), int(rgb[2]*255)))
                win32gui.SelectObject(hdc_handle, brush)
                win32gui.BitBlt(hdc_handle, random.randint(-20, 20), random.randint(-20, 20), sw, sh, hdc_handle, 0, 0, win32con.PATINVERT)
                win32gui.DeleteObject(brush)
                draw_gradient_triangle(hdc_handle, sw, sh)
                hue += 0.05

        elif 20 < elapsed <= 40:
            hue += 0.02
            for i in range(0, (sw * sh) - 1, 2):
                x, y = i % sw, i // sw
                checker = ((x + move_offset) >> 6) ^ ((y + move_offset) >> 6)
                r_comp = int((math.sin(hue) * 127 + 128)) ^ (checker * 255)
                g_comp = int((math.sin(hue + 2) * 127 + 128)) ^ (checker * 255)
                b_comp = int((math.sin(hue + 4) * 127 + 128)) ^ (checker * 255)
                pixel_array[i] = (r_comp & 0xFF) | ((g_comp & 0xFF) << 8) | ((b_comp & 0xFF) << 16)
                pixel_array[i+1] = pixel_array[i]
            hdc_screen_ui.BitBlt((0, 0), (sw, sh), hdc_mem, (0, 0), win32con.SRCCOPY)
            move_offset += 15

        elif 40 < elapsed <= 60:
            rgb = colorsys.hsv_to_rgb(hue % 1.0, 1.0, 1.0)
            brush = win32gui.CreateSolidBrush(win32api.RGB(int(rgb[0]*255), int(rgb[1]*255), int(rgb[2]*255)))
            win32gui.SelectObject(hdc_handle, brush)
            for i in range(0, int(sw + sh), scaling_factor):
                a = int(math.sin(sine_angle + i/100) * 20 * scaling_factor)
                win32gui.BitBlt(hdc_handle, 0, i, sw, scaling_factor, hdc_handle, a, i, win32con.SRCCOPY)
                if i % 20 == 0:
                    win32gui.BitBlt(hdc_handle, 0, i, sw, scaling_factor, hdc_handle, a, i, win32con.PATINVERT)
            win32gui.DeleteObject(brush)
            sine_angle += 0.1
            hue += 0.02

        else:
            current_dc_ui.SelectObject(massive_font)
            rgb = colorsys.hsv_to_rgb(hue % 1.0, 1.0, 1.0)
            current_dc_ui.SetTextColor(win32api.RGB(int(rgb[0]*255), int(rgb[1]*255), int(rgb[2]*255)))
            current_dc_ui.SetBkMode(win32con.TRANSPARENT)
            for _ in range(10):
                tx, ty = random.randint(-100, sw), random.randint(-100, sh)
                current_dc_ui.TextOut(tx, ty, "peliy11")
            hue += 0.03

        win32gui.ReleaseDC(0, hdc_handle)
        time.sleep(0.001)

except KeyboardInterrupt:
    winsound.PlaySound(None, winsound.SND_PURGE)
    win32gui.ReleaseDC(0, hdc_screen_init)
