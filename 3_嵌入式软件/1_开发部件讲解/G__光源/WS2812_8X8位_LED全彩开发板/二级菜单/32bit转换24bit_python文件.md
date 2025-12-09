


```python
import tkinter as tk  
from tkinter import filedialog, messagebox, scrolledtext  
import re  
import os  
import threading  
  
  
class ColorConverterApp:  
    def __init__(self, root):  
        self.root = root  
        self.root.title("LED Matrix Studio 32-bit to 24-bit Converter")  
        self.root.geometry("700x600")  
        self.root.resizable(True, True)  
  
        # 设置应用图标  
        try:  
            self.root.iconbitmap("icon.ico")  
        except:  
            pass  
  
        # 创建UI元素  
        self.create_widgets()  
  
        # 默认值  
        self.input_file = ""  
        self.output_file = ""  
  
    def create_widgets(self):  
        # 创建主框架  
        main_frame = tk.Frame(self.root, padx=20, pady=20)  
        main_frame.pack(fill=tk.BOTH, expand=True)  
  
        # 标题  
        title_label = tk.Label(  
            main_frame,  
            text="LED Matrix Studio 32-bit to 24-bit Color Converter",  
            font=("Arial", 16, "bold"),  
            fg="#2c3e50"  
        )  
        title_label.pack(pady=(0, 20))  
  
        # 输入文件部分  
        input_frame = tk.LabelFrame(main_frame, text="Input File", padx=10, pady=10)  
        input_frame.pack(fill=tk.X, pady=10)  
  
        self.input_entry = tk.Entry(input_frame, width=60)  
        self.input_entry.grid(row=0, column=0, padx=5, pady=5, sticky="ew")  
  
        input_btn = tk.Button(  
            input_frame,  
            text="Browse...",  
            command=self.browse_input_file,  
            bg="#3498db",  
            fg="white"  
        )  
        input_btn.grid(row=0, column=1, padx=5, pady=5)  
  
        # 输出文件部分  
        output_frame = tk.LabelFrame(main_frame, text="Output File", padx=10, pady=10)  
        output_frame.pack(fill=tk.X, pady=10)  
  
        self.output_entry = tk.Entry(output_frame, width=60)  
        self.output_entry.grid(row=0, column=0, padx=5, pady=5, sticky="ew")  
  
        output_btn = tk.Button(  
            output_frame,  
            text="Browse...",  
            command=self.browse_output_file,  
            bg="#3498db",  
            fg="white"  
        )  
        output_btn.grid(row=0, column=1, padx=5, pady=5)  
  
        # 转换按钮  
        convert_btn = tk.Button(  
            main_frame,  
            text="Convert Colors",  
            command=self.start_conversion,  
            bg="#27ae60",  
            fg="white",  
            font=("Arial", 12, "bold"),  
            height=2  
        )  
        convert_btn.pack(pady=20, fill=tk.X)  
  
        # 日志区域  
        log_frame = tk.LabelFrame(main_frame, text="Conversion Log", padx=10, pady=10)  
        log_frame.pack(fill=tk.BOTH, expand=True, pady=10)  
  
        self.log_text = scrolledtext.ScrolledText(  
            log_frame,  
            wrap=tk.WORD,  
            height=15,  
            font=("Consolas", 10)  
        )  
        self.log_text.pack(fill=tk.BOTH, expand=True)  
        self.log_text.config(state=tk.DISABLED)  
  
        # 状态栏  
        self.status_var = tk.StringVar()  
        self.status_var.set("Ready")  
        status_bar = tk.Label(  
            self.root,  
            textvariable=self.status_var,  
            bd=1,  
            relief=tk.SUNKEN,  
            anchor=tk.W,  
            padx=10  
        )  
        status_bar.pack(side=tk.BOTTOM, fill=tk.X)  
  
    def browse_input_file(self):  
        file_path = filedialog.askopenfilename(  
            title="Select Input File",  
            filetypes=[("C++ Files", "*.cpp *.h"), ("All Files", "*.*")]  
        )  
        if file_path:  
            self.input_file = file_path  
            self.input_entry.delete(0, tk.END)  
            self.input_entry.insert(0, file_path)  
  
            # 自动生成输出文件名  
            if not self.output_entry.get():  
                dir_name, file_name = os.path.split(file_path)  
                base_name, ext = os.path.splitext(file_name)  
                output_path = os.path.join(dir_name, f"{base_name}_converted{ext}")  
                self.output_entry.delete(0, tk.END)  
                self.output_entry.insert(0, output_path)  
  
    def browse_output_file(self):  
        file_path = filedialog.asksaveasfilename(  
            title="Save Output File",  
            defaultextension=".cpp",  
            filetypes=[("C++ Files", "*.cpp *.h"), ("All Files", "*.*")]  
        )  
        if file_path:  
            self.output_file = file_path  
            self.output_entry.delete(0, tk.END)  
            self.output_entry.insert(0, file_path)  
  
    def start_conversion(self):  
        input_path = self.input_entry.get()  
        output_path = self.output_entry.get()  
  
        if not input_path:  
            messagebox.showerror("Error", "Please select an input file")  
            return  
  
        if not output_path:  
            messagebox.showerror("Error", "Please select an output file")  
            return  
  
        # 禁用转换按钮并更新状态  
        self.status_var.set("Converting... Please wait")  
        self.root.update()  
  
        # 在新线程中运行转换以避免UI冻结  
        threading.Thread(  
            target=self.convert_colors,  
            args=(input_path, output_path),  
            daemon=True  
        ).start()  
  
    def convert_colors(self, input_path, output_path):  
        try:  
            # 读取输入文件  
            with open(input_path, 'r') as f:  
                content = f.read()  
  
            # 清空日志  
            self.log_text.config(state=tk.NORMAL)  
            self.log_text.delete(1.0, tk.END)  
  
            # 添加日志头  
            self.add_log("Color Conversion Started")  
            self.add_log(f"Input File: {input_path}")  
            self.add_log(f"Output File: {output_path}")  
            self.add_log("-" * 60)  
  
            # 查找所有数组  
            array_pattern = re.compile(  
                r'(const uint32_t\s+(\w+)\s*\[\]\s*PROGMEM\s*=\s*\{)([^;]*?)(\};)',  
                re.DOTALL  
            )  
  
            converted_content = content  
            array_count = 0  
            color_count = 0  
  
            # 处理所有匹配的数组  
            for match in array_pattern.finditer(content):  
                array_decl = match.group(1)  
                array_name = match.group(2)  
                array_body = match.group(3)  
                array_end = match.group(4)  
  
                self.add_log(f"Processing array: {array_name}")  
  
                # 处理数组体中的颜色值  
                color_pattern = re.compile(r'0x[0-9a-fA-F]{8}')  
                new_body = color_pattern.sub(self.convert_color, array_body)  
  
                # 创建新的数组内容  
                new_array = f"{array_decl}{new_body}{array_end}"  
                converted_content = converted_content.replace(match.group(0), new_array)  
  
                # 计算处理的颜色数量  
                colors_in_array = len(color_pattern.findall(array_body))  
                color_count += colors_in_array  
                array_count += 1  
  
                self.add_log(f"  Converted {colors_in_array} color values")  
  
            # 写入输出文件  
            with open(output_path, 'w') as f:  
                f.write(converted_content)  
  
            # 更新日志  
            self.add_log("-" * 60)  
            self.add_log(f"Conversion Complete!")  
            self.add_log(f"Processed {array_count} arrays")  
            self.add_log(f"Converted {color_count} color values")  
            self.add_log(f"Output saved to: {output_path}")  
  
            self.status_var.set("Conversion completed successfully")  
  
            # 询问是否打开输出文件  
            if messagebox.askyesno(  
                    "Success",  
                    "Conversion completed successfully!\n\nWould you like to open the output file?"  
            ):  
                os.startfile(output_path)  
  
        except Exception as e:  
            self.add_log(f"Error: {str(e)}")  
            self.status_var.set("Conversion failed")  
            messagebox.showerror("Error", f"An error occurred during conversion:\n\n{str(e)}")  
  
    def convert_color(self, match):  
        """将32位颜色值转换为24位格式"""  
        color_hex = match.group(0)  
        try:  
            # 提取32位颜色值 (0x00RRGGBB)            color_val = int(color_hex, 16)  
            # 转换为24位格式 (0xRRGGBB)            color_24bit = color_val & 0x00FFFFFF  
            return f"0x{color_24bit:06X}"  
        except:  
            return color_hex  
  
    def add_log(self, message):  
        """向日志区域添加消息"""  
        self.log_text.config(state=tk.NORMAL)  
        self.log_text.insert(tk.END, message + "\n")  
        self.log_text.see(tk.END)  
        self.log_text.config(state=tk.DISABLED)  
        self.root.update()  
  
  
if __name__ == "__main__":  
    root = tk.Tk()  
    app = ColorConverterApp(root)  
    root.mainloop()
```

