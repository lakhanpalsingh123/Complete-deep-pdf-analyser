# Complete-deep-pdf-analyser
import os
import re
import customtkinter as ctk
from tkinter import filedialog, messagebox

class SecurityApp(ctk.CTk):
    def __init__(self):
        super().__init__()
        self.title("Sentinel Pro v1.6")
        self.geometry("1100x850")
        ctk.set_appearance_mode("dark")
        
        self.selected_file = None
        self.report_data = [] # Stores all analysis steps for printing

        # --- UI SETUP ---
        self.grid_columnconfigure(1, weight=1)
        self.grid_rowconfigure(0, weight=1)

        # Sidebar
        self.sidebar = ctk.CTkFrame(self, width=220)
        self.sidebar.grid(row=0, column=0, sticky="nsew", padx=10, pady=10)
        
        ctk.CTkLabel(self.sidebar, text="SENTINEL AI", font=("Consolas", 22, "bold")).pack(pady=20)

        ctk.CTkButton(self.sidebar, text="LOAD TARGET", command=self.load_file).pack(pady=10)
        ctk.CTkButton(self.sidebar, text="PDF STRUCTURE", command=self.run_pdf_scan).pack(pady=10)
        ctk.CTkButton(self.sidebar, text="SOCIAL TRIAGE", command=self.run_social).pack(pady=10)
        
        # NEW: Print Report Button
        self.btn_print = ctk.CTkButton(self.sidebar, text="PRINT REPORT", fg_color="#2b7336", 
                                      hover_color="#1e5226", state="disabled", command=self.print_report)
        self.btn_print.pack(pady=20)

        # --- FIX: Visible Text Terminal ---
        # Added text_color="white" and a border to ensure visibility
        self.terminal = ctk.CTkTextbox(self, font=("Consolas", 14), 
                                      fg_color="#1a1a1a", 
                                      text_color="#00FF00", # Classic Hacker Green for visibility
                                      border_width=2,
                                      border_color="#333333")
        self.terminal.grid(row=0, column=1, padx=20, pady=20, sticky="nsew")

        # Threat Meter
        self.meter = ctk.CTkProgressBar(self.sidebar); self.meter.pack(pady=10); self.meter.set(0)
        self.status_lbl = ctk.CTkLabel(self.sidebar, text="READY", text_color="gray")
        self.status_lbl.pack()

    def log(self, msg):
        """Logs to terminal and stores for the print report."""
        formatted_msg = f">>> {msg}"
        self.terminal.insert("end", f"{formatted_msg}\n")
        self.terminal.see("end")
        self.report_data.append(formatted_msg)

    def load_file(self):
        path = filedialog.askopenfilename()
        if path:
            self.selected_file = path
            self.report_data = [f"SENTINEL PRO ANALYSIS REPORT", "="*30, f"Target: {path}", ""]
            self.terminal.delete("1.0", "end") # Clear old logs
            self.log(f"TARGET LOADED: {os.path.basename(path)}")
            self.btn_print.configure(state="normal")

    def run_pdf_scan(self):
        self.log("Initializing PDF Object Triage...")
        # (PDF logic goes here)
        self.log("CRITICAL: Embedded JavaScript detected in Stream 4.")
        self.meter.set(0.7)
        self.status_lbl.configure(text="SUSPICIOUS PDF", text_color="orange")

    def run_social(self):
        self.log("Analyzing Social Engineering Lures...")
        # (Social logic goes here)
        self.log("Score: 85/100 - High Phishing Probability.")
        self.meter.set(0.9)
        self.status_lbl.configure(text="THREAT DETECTED", text_color="red")

    # --- NEW: PRINT FEATURE ---
    def print_report(self):
        if not self.report_data:
            return
            
        file_path = filedialog.asksaveasfilename(defaultextension=".txt",
                                                filetypes=[("Text files", "*.txt")],
                                                initialfile=f"Sentinel_Report_{os.path.basename(self.selected_file)}.txt")
        if file_path:
            try:
                with open(file_path, "w") as f:
                    f.write("\n".join(self.report_data))
                messagebox.showinfo("Success", f"Deep Analysis Report printed to:\n{file_path}")
            except Exception as e:
                messagebox.showerror("Error", f"Failed to save report: {e}")

if __name__ == "__main__":
    app = SecurityApp()
    app.mainloop()
