# Ti_Calculator

Hi, this is the first app that I made using python.
This was designed based on my calculator in middle school.

import tkinter as tk
from tkinter import ttk
import math
import cmath

class TICalculator:
    def __init__(self, root):
        self.root = root
        self.root.title("TI Scientific Calculator")
        self.root.geometry("400x600")
        self.root.configure(bg='#2c3e50')
        self.root.resizable(False, False)
        
        # Calculator state
        self.current = "0"
        self.previous = ""
        self.operator = ""
        self.result = 0
        self.memory = 0
        self.angle_mode = "deg"  # deg or rad
        self.second_function = False
        
        self.create_display()
        self.create_buttons()

    def create_display(self):
        # Display frame
        display_frame = tk.Frame(self.root, bg='#34495e', pady=10)
        display_frame.pack(fill=tk.X, padx=10)
        
        # Secondary display (for operations)
        self.secondary_display = tk.Label(
            display_frame, 
            text="", 
            font=('Arial', 10), 
            bg='#34495e', 
            fg='white',
            anchor='e',
            height=1
        )
        self.secondary_display.pack(fill=tk.X, pady=(0, 2))
        
        # Main display
        self.display = tk.Label(
            display_frame, 
            text="0", 
            font=('Arial', 16, 'bold'), 
            bg='#34495e', 
            fg='#00ff00',
            anchor='e',
            height=2,
            relief='sunken',
            bd=2
        )
        self.display.pack(fill=tk.X)
        
        # Mode indicator
        self.mode_display = tk.Label(
            display_frame,
            text="DEG",
            font=('Arial', 8),
            bg='#34495e',
            fg='yellow'
        )
        self.mode_display.pack(anchor='w')
    
    def create_buttons(self):
        button_frame = tk.Frame(self.root, bg='#34495e')
        button_frame.pack(fill=tk.BOTH, expand=True, padx=10, pady=5)
        
        # Button configuration
        btn_config = {
            'font': ('Arial', 10, 'bold'),
            'width': 6,
            'height': 2,
            'relief': 'raised',
            'bd': 2,
            'highlightthickness': 0  # Removes the border around the button when clicked
        }
        
        # Function button style
        func_style = {**btn_config, 'bg': '#3498db', 'fg': 'white', 'activebackground': '#2980b9'}
        # Number button style  
        num_style = {**btn_config, 'bg': '#ecf0f1', 'fg': 'black', 'activebackground': '#bdc3c7'}
        # Operator button style
        op_style = {**btn_config, 'bg': '#e74c3c', 'fg': 'white', 'activebackground': '#c0392b'}
        # Special button style
        spec_style = {**btn_config, 'bg': '#f39c12', 'fg': 'white', 'activebackground': '#e67e22'}
        
        # Row 0: 2nd, MODE, DEL, ON/AC
        tk.Button(button_frame, text="2nd", command=self.toggle_2nd, **spec_style).grid(row=0, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="MODE", command=self.toggle_mode, **func_style).grid(row=0, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="DEL", command=self.delete, **func_style).grid(row=0, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="ON/AC", command=self.clear_all, **op_style).grid(row=0, column=3, padx=1, pady=1)
        
        # Row 1: x², x³, x^y, √
        tk.Button(button_frame, text="x²", command=lambda: self.function('square'), **func_style).grid(row=1, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="x³", command=lambda: self.function('cube'), **func_style).grid(row=1, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="x^y", command=lambda: self.operator_press('^'), **func_style).grid(row=1, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="√", command=lambda: self.function('sqrt'), **func_style).grid(row=1, column=3, padx=1, pady=1)
        
        # Row 2: sin, cos, tan, ln
        tk.Button(button_frame, text="sin", command=lambda: self.function('sin'), **func_style).grid(row=2, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="cos", command=lambda: self.function('cos'), **func_style).grid(row=2, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="tan", command=lambda: self.function('tan'), **func_style).grid(row=2, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="ln", command=lambda: self.function('ln'), **func_style).grid(row=2, column=3, padx=1, pady=1)
        
        # Row 3: log, π, e, ÷
        tk.Button(button_frame, text="log", command=lambda: self.function('log'), **func_style).grid(row=3, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="π", command=lambda: self.number_press(str(math.pi)), **func_style).grid(row=3, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="e", command=lambda: self.number_press(str(math.e)), **func_style).grid(row=3, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="÷", command=lambda: self.operator_press('/'), **op_style).grid(row=3, column=3, padx=1, pady=1)
        
        # Row 4: 7, 8, 9, ×
        tk.Button(button_frame, text="7", command=lambda: self.number_press('7'), **num_style).grid(row=4, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="8", command=lambda: self.number_press('8'), **num_style).grid(row=4, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="9", command=lambda: self.number_press('9'), **num_style).grid(row=4, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="×", command=lambda: self.operator_press('*'), **op_style).grid(row=4, column=3, padx=1, pady=1)
        
        # Row 5: 4, 5, 6, -
        tk.Button(button_frame, text="4", command=lambda: self.number_press('4'), **num_style).grid(row=5, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="5", command=lambda: self.number_press('5'), **num_style).grid(row=5, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="6", command=lambda: self.number_press('6'), **num_style).grid(row=5, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="−", command=lambda: self.operator_press('-'), **op_style).grid(row=5, column=3, padx=1, pady=1)
        
        # Row 6: 1, 2, 3, +
        tk.Button(button_frame, text="1", command=lambda: self.number_press('1'), **num_style).grid(row=6, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="2", command=lambda: self.number_press('2'), **num_style).grid(row=6, column=1, padx=1, pady=1)
        tk.Button(button_frame, text="3", command=lambda: self.number_press('3'), **num_style).grid(row=6, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="+", command=lambda: self.operator_press('+'), **op_style).grid(row=6, column=3, padx=1, pady=1)
        
        # Row 7: ±, 0, ., =
        tk.Button(button_frame, text="±", command=lambda: self.toggle_sign(), **num_style).grid(row=7, column=0, padx=1, pady=1)
        tk.Button(button_frame, text="0", command=lambda: self.number_press('0'), **num_style).grid(row=7, column=1, padx=1, pady=1)
        tk.Button(button_frame, text=".", command=lambda: self.number_press('.'), **num_style).grid(row=7, column=2, padx=1, pady=1)
        tk.Button(button_frame, text="=", command=self.equals, **op_style).grid(row=7, column=3, padx=1, pady=1)

    def update_display(self):
        self.display.config(text=self.current)

    def update_secondary_display(self, text=""):
        self.secondary_display.config(text=text)

    def number_press(self, num):
        if self.current == "0" or self.current == "Error":
            self.current = num
        else:
            self.current += num
        self.update_display()

    def delete(self):
        if len(self.current) > 1:
            self.current = self.current[:-1]  # Remove the last character
        else:
            self.current = "0"  # If only one character remains, reset to 0
        self.update_display()


    def operator_press(self, op):
        if self.operator and self.previous:
            self.equals()

        self.previous = self.current
        self.operator = op
        self.current = "0"
        self.update_secondary_display(f"{self.previous} {op}")

    def function(self, func):
        try:
            val = float(self.current)

            if func == 'square':
                result = val ** 2
            elif func == 'cube':
                result = val ** 3
            elif func == 'sqrt':
                if val < 0:
                    raise ValueError
                result = math.sqrt(val)
            elif func == 'sin':
                result = math.sin(math.radians(val) if self.angle_mode == "deg" else val)
            elif func == 'cos':
                result = math.cos(math.radians(val) if self.angle_mode == "deg" else val)
            elif func == 'tan':
                result = math.tan(math.radians(val) if self.angle_mode == "deg" else val)
            elif func == 'log':
                if val <= 0:
                    raise ValueError
                result = math.log(val)
            elif func == 'ln':
                if val <= 0:
                    raise ValueError
                result = math.log(val, math.e)
            self.current = str(result)
            self.update_display()

        except ValueError:
            self.current = "Error"
            self.update_display()

    def equals(self):
        try:
            if self.operator and self.previous:
                prev_val = float(self.previous)
                curr_val = float(self.current)

                if self.operator == '+':
                    result = prev_val + curr_val
                elif self.operator == '-':
                    result = prev_val - curr_val
                elif self.operator == '*':
                    result = prev_val * curr_val
                elif self.operator == '/':
                    if curr_val == 0:
                        raise ZeroDivisionError
                    result = prev_val / curr_val
                elif self.operator == '^':
                    result = prev_val ** curr_val

                self.current = str(result)
                self.previous = ""
                self.operator = ""
                self.update_secondary_display()
                self.update_display()

        except (ValueError, ZeroDivisionError):
            self.current = "Error"
            self.update_display()

    def clear_all(self):
        self.current = "0"
        self.previous = ""
        self.operator = ""
        self.update_display()
        self.update_secondary_display()

    def toggle_2nd(self):
        # Toggle the 2nd function (advanced mode functionality)
        self.second_function = not self.second_function

    def toggle_mode(self):
        # Toggle between degrees and radians mode
        if self.angle_mode == "deg":
            self.angle_mode = "rad"
            self.mode_display.config(text="RAD")
        else:
            self.angle_mode = "deg"
            self.mode_display.config(text="DEG")

    def toggle_sign(self):
        if self.current != "0":
            if self.current.startswith('-'):
                self.current = self.current[1:]
            else:
                self.current = '-' + self.current
            self.update_display()

if __name__ == "__main__":
    root = tk.Tk()
    calculator = TICalculator(root)
    root.mainloop()
