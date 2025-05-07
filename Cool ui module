import tkinter as tk
from tkinter import ttk, messagebox
from PIL import Image, ImageTk
import sqlite3
import random
import requests
import json
import time

# --- Colors and settings ---
BG_COLOR = "#1D223A"
ENTRY_COLOR = "#292E4A"
TEXT_COLOR = "#B8C2E0"
LABEL_COLOR = "#FFFFFF"
BUTTON_COLOR = "#7A5CFA"
ACCENT_COLOR = "#9D8DF2"
BORDER_RADIUS = 15
WIDTH = 700
HEIGHT = 600
NAV_HEIGHT = 50  # Height of the navigation bar
ICON_SIZE = 24   # Size of the icons in the navigation bar

class LoginScreen:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("Login")
        self.root.geometry("400x400")
        self.root.configure(bg=BG_COLOR)

        # Canvas with rounded corners
        self.canvas = tk.Canvas(self.root, width=300, height=270, 
                              bg=BG_COLOR, highlightthickness=0)
        self.canvas.place(x=50, y=40)

        self.create_rounded_background()
        self.setup_login_ui()

    def create_rounded_background(self):
        points = [
            BORDER_RADIUS, 0,
            300 - BORDER_RADIUS, 0,
            300, BORDER_RADIUS,
            300, 270 - BORDER_RADIUS,
            300 - BORDER_RADIUS, 270,
            BORDER_RADIUS, 270,
            0, 270 - BORDER_RADIUS,
            0, BORDER_RADIUS
        ]
        self.canvas.create_polygon(points, fill=ENTRY_COLOR, smooth=True)

    def setup_login_ui(self):
        # Login Title
        self.canvas.create_text(25, 25, text="Login", anchor="w", 
                              fill=LABEL_COLOR, font=("Segoe UI", 16, "bold"))

        # Username Entry with placeholder
        self.username_entry = tk.Entry(self.root, bd=0, bg=ENTRY_COLOR, 
                                     fg=TEXT_COLOR, font=("Segoe UI", 12),
                                     insertbackground=TEXT_COLOR, highlightthickness=0)
        self.username_entry.place(x=80, y=85, width=180, height=25)
        self.username_entry.insert(0, "Username")
        
        # Password Entry with placeholder
        self.password_entry = tk.Entry(self.root, bd=0, bg=ENTRY_COLOR,
                                     fg=TEXT_COLOR, font=("Segoe UI", 12),
                                     insertbackground=TEXT_COLOR, highlightthickness=0, show="•")
        self.password_entry.place(x=80, y=135, width=180, height=25)
        self.password_entry.insert(0, "Password")
        self.password_entry.config(show="")
        
        # Login Button
        self.login_btn = tk.Button(self.root, text="Login", bg=BUTTON_COLOR,
                                  fg="white", font=("Segoe UI", 12),
                                  command=self.verify_login, bd=0, relief=tk.FLAT,
                                  cursor="hand2", highlightthickness=0,
                                  highlightbackground=BG_COLOR, highlightcolor=BG_COLOR,
                                  activebackground=ACCENT_COLOR, activeforeground="white")
        self.login_btn.place(x=120, y=210, width=100, height=35)

        # Add focus events for placeholder text
        self.username_entry.bind('<FocusIn>', self.on_username_focus_in)
        self.username_entry.bind('<FocusOut>', self.on_username_focus_out)
        self.password_entry.bind('<FocusIn>', self.on_password_focus_in)
        self.password_entry.bind('<FocusOut>', self.on_password_focus_out)

    def on_username_focus_in(self, _):
        if self.username_entry.get() == "Username":
            self.username_entry.delete(0, tk.END)
            self.username_entry.config(fg=TEXT_COLOR)

    def on_username_focus_out(self, _):
        if self.username_entry.get() == "":
            self.username_entry.insert(0, "Username")
            self.username_entry.config(fg=TEXT_COLOR)

    def on_password_focus_in(self, _):
        if self.password_entry.get() == "Password":
            self.password_entry.delete(0, tk.END)
            self.password_entry.config(show="•", fg=TEXT_COLOR)

    def on_password_focus_out(self, _):
        if self.password_entry.get() == "":
            self.password_entry.insert(0, "Password")
            self.password_entry.config(show="", fg=TEXT_COLOR)

    def verify_login(self):
        username = self.username_entry.get()
        password = self.password_entry.get()
        
        # Don't verify if placeholder text is still there
        if username == "Username" or password == "Password":
            messagebox.showerror("Error", "Please enter your credentials!")
            return
        
        # Username is case-insensitive, password is case-sensitive
        if username.lower() == "alimistheman".lower() and password == "Alim1234":
            self.root.destroy()
            app = FactApp()
            app.run()
        else:
            messagebox.showerror("Error", "Invalid credentials!")

class FactApp:
    def __init__(self):
        self.root = tk.Tk()
        self.root.title("Random Facts Generator")
        self.root.geometry(f"{WIDTH}x{HEIGHT}")
        self.root.configure(bg=BG_COLOR)

        # Disable resizing the window
        self.root.resizable(False, False)
        
        # Initialize database
        self.setup_database()
        
        # Main content frame
        self.content_frame = tk.Frame(self.root, bg=BG_COLOR)
        self.content_frame.pack(expand=True, fill="both", side="top")

        # Navigation bar frame
        self.nav_frame = tk.Frame(self.root, bg=ENTRY_COLOR, height=NAV_HEIGHT)
        self.nav_frame.pack(fill="x", side="bottom")
        
        # Initialize frames
        self.main_frame = None
        self.settings_frame = None
        self.favorites_frame = None
        self.dashboard_frame = None
        
        # Load icons
        self.load_icons()
        
        # Setup UI elements
        self.setup_ui()
        self.setup_settings_ui()
        self.setup_favorites_ui()
        self.setup_dashboard_ui()
        self.setup_navigation()
        
        # Load settings
        self.load_settings()
        
        # Load initial fact
        self.current_fact = ""
        self.get_new_fact()
        
        # Show initial frame
        self.show_frame(self.main_frame)

    def load_icons(self):
        # Load the icons
        try:
            self.fact_icon = ImageTk.PhotoImage(Image.open("lightbulb.png").resize((ICON_SIZE, ICON_SIZE), Image.LANCZOS))
            self.favorites_icon = ImageTk.PhotoImage(Image.open("heart.png").resize((ICON_SIZE, ICON_SIZE), Image.LANCZOS))
            self.dashboard_icon = ImageTk.PhotoImage(Image.open("dashboard.png").resize((ICON_SIZE, ICON_SIZE), Image.LANCZOS))
            self.settings_icon = ImageTk.PhotoImage(Image.open("settings.png").resize((ICON_SIZE, ICON_SIZE), Image.LANCZOS))
        except FileNotFoundError:
            messagebox.showerror("Error", "One or more icon files not found. Please make sure 'lightbulb.png', 'heart.png', 'dashboard.png', and 'settings.png' are in the same directory as the script.")
            self.fact_icon = None
            self.favorites_icon = None
            self.dashboard_icon = None
            self.settings_icon = None

    def setup_navigation(self):
        # Define navigation buttons with icons
        nav_buttons = [
            ("Facts", self.show_main_frame, self.fact_icon),
            ("Favorites", self.show_favorites_frame, self.favorites_icon),
            ("Dashboard", self.show_dashboard_frame, self.dashboard_icon),
            ("Settings", self.show_settings_frame, self.settings_icon)
        ]
        
        # Calculate button width
        button_width = WIDTH / len(nav_buttons)
        
        # Create and place navigation buttons
        for i, (text, command, icon) in enumerate(nav_buttons):
            if icon:
                nav_button = tk.Button(self.nav_frame, text=text, image=icon, compound="left",
                                        bg=ENTRY_COLOR, fg=TEXT_COLOR,
                                        font=("Segoe UI", 12),
                                        command=command, bd=0, relief=tk.FLAT,
                                        cursor="hand2", highlightthickness=0,
                                        highlightbackground=ENTRY_COLOR, highlightcolor=ENTRY_COLOR,
                                        activebackground=ACCENT_COLOR, activeforeground="white",
                                        width=int(button_width/10))  # Adjust width as needed
            else:
                nav_button = tk.Button(self.nav_frame, text=text,
                                        bg=ENTRY_COLOR, fg=TEXT_COLOR,
                                        font=("Segoe UI", 12),
                                        command=command, bd=0, relief=tk.FLAT,
                                        cursor="hand2", highlightthickness=0,
                                        highlightbackground=ENTRY_COLOR, highlightcolor=ENTRY_COLOR,
                                        activebackground=ACCENT_COLOR, activeforeground="white",
                                        width=int(button_width/10))  # Adjust width as needed
            nav_button.pack(side="left", fill="both", expand=True)

    def show_frame(self, frame):
        # Hide all frames
        if self.main_frame:
            self.main_frame.pack_forget()
        if self.settings_frame:
            self.settings_frame.pack_forget()
        if self.favorites_frame:
            self.favorites_frame.pack_forget()
        if self.dashboard_frame:
            self.dashboard_frame.pack_forget()
        
        # Show the selected frame
        frame.pack(expand=True, fill="both")

    def show_main_frame(self):
        self.show_frame(self.main_frame)

    def show_settings_frame(self):
        self.show_frame(self.settings_frame)

    def show_favorites_frame(self):
        self.show_frame(self.favorites_frame)

    def show_dashboard_frame(self):
        self.show_frame(self.dashboard_frame)

    def setup_ui(self):
        # Main frame
        self.main_frame = tk.Frame(self.content_frame, bg=BG_COLOR)
        
        # Rounded background
        self.canvas = tk.Canvas(self.main_frame, width=WIDTH, height=HEIGHT - NAV_HEIGHT, 
                              bg=BG_COLOR, highlightthickness=0)
        self.canvas.pack(padx=10, pady=10)
        self.create_rounded_background(self.canvas, WIDTH, HEIGHT - NAV_HEIGHT)
        
        # Title
        self.canvas.create_text(WIDTH/2, 40, text="Random Facts Generator",
                              fill=LABEL_COLOR, font=("Segoe UI", 26, "bold"))
        
        # Fact display area
        self.fact_text = tk.Text(self.main_frame, wrap=tk.WORD, width=50, height=8,
                                bg=ENTRY_COLOR, fg=TEXT_COLOR, font=("Segoe UI", 14),
                                bd=0, padx=10, pady=10, state="disabled",
                                highlightthickness=0)
        self.fact_text.place(x=100, y=150)
        
        # Category label
        self.category_label = tk.Label(self.main_frame, text="Category: General",
                                     bg=BG_COLOR, fg=TEXT_COLOR, font=("Segoe UI", 14))
        self.category_label.place(x=100, y=300)
        
        # New Fact Button with rounded corners
        self.new_fact_btn_canvas = tk.Canvas(self.main_frame, width=150, height=40,
                                            bg=BG_COLOR, highlightthickness=0)
        self.new_fact_btn_canvas.place(x=100, y=350)
        
        # Draw rounded rectangle on canvas
        self.draw_rounded_rectangle(self.new_fact_btn_canvas, 0, 0, 150, 40, BORDER_RADIUS,
                                    fill=BUTTON_COLOR)
        
        self.new_fact_btn = tk.Button(self.main_frame, text="New Fact", bg=BUTTON_COLOR,
                                     fg="white", font=("Segoe UI", 14), bd=0,
                                     command=self.get_new_fact, relief=tk.FLAT,
                                     cursor="hand2", highlightthickness=0,
                                     highlightbackground=BG_COLOR, highlightcolor=BG_COLOR,
                                     activebackground=ACCENT_COLOR, activeforeground="white")
        self.new_fact_btn.place(x=100, y=350, width=150, height=40)
        
        # Bind hover animation
        self.new_fact_btn.bind("<Enter>", lambda event: self.on_enter(event, 160, 45))
        self.new_fact_btn.bind("<Leave>", lambda event: self.on_leave(event, 150, 40))
        
        # Make button transparent
        self.new_fact_btn.config(bg=BUTTON_COLOR, fg="white", bd=0,
                                 highlightbackground=BG_COLOR, highlightcolor=BG_COLOR,
                                 activebackground=ACCENT_COLOR, activeforeground="white")
        
        # Save Fact Button
        self.save_fact_btn = tk.Button(self.main_frame, text="Save Fact", bg=BUTTON_COLOR,
                                      fg="white", font=("Segoe UI", 14),
                                      command=self.save_fact, bd=0, relief=tk.FLAT,
                                      cursor="hand2", highlightthickness=0,
                                      highlightbackground=BG_COLOR, highlightcolor=BG_COLOR,
                                      activebackground=ACCENT_COLOR, activeforeground="white")
        self.save_fact_btn.place(x=270, y=350, width=150, height=40)
        
        # Bind hover animation
        self.save_fact_btn.bind("<Enter>", lambda event: self.on_enter(event, 160, 45))
        self.save_fact_btn.bind("<Leave>", lambda event: self.on_leave(event, 150, 40))

    def draw_rounded_rectangle(self, canvas, x1, y1, x2, y2, radius, **kwargs):
        points = [x1+radius, y1,
                  x2-radius, y1,
                  x2, y1,
                  x2, y1+radius,
                  x2, y2-radius,
                  x2, y2,
                  x2-radius, y2,
                  x1+radius, y2,
                  x1, y2,
                  x1, y2-radius,
                  x1, y1+radius,
                  x1, y1]
        canvas.create_polygon(points, smooth=True, **kwargs)

    def setup_settings_ui(self):
        # Settings frame
        self.settings_frame = tk.Frame(self.content_frame, bg=BG_COLOR)
        
        # Theme selection
        self.theme_label = tk.Label(self.settings_frame, text="Theme:",
                                  bg=BG_COLOR, fg=TEXT_COLOR,
                                  font=("Segoe UI", 14))
        self.theme_label.pack(pady=10)
        
        self.theme_var = tk.StringVar(value="dark")
        self.theme_dark = tk.Radiobutton(self.settings_frame, text="Dark",
                                        variable=self.theme_var, value="dark",
                                        command=self.save_settings,
                                        bg=BG_COLOR, fg=TEXT_COLOR, selectcolor=ENTRY_COLOR,
                                        highlightthickness=0, bd=0, font=("Segoe UI", 12))
        self.theme_light = tk.Radiobutton(self.settings_frame, text="Light",
                                         variable=self.theme_var, value="light",
                                         command=self.save_settings,
                                         bg=BG_COLOR, fg=TEXT_COLOR, selectcolor=ENTRY_COLOR,
                                         highlightthickness=0, bd=0, font=("Segoe UI", 12))
        self.theme_dark.pack()
        self.theme_light.pack()

    def setup_favorites_ui(self):
        # Favorites frame
        self.favorites_frame = tk.Frame(self.content_frame, bg=BG_COLOR)
        
        # Use a frame to hold the facts and remove buttons
        self.facts_frame = tk.Frame(self.favorites_frame, bg=BG_COLOR)
        self.facts_frame.pack(expand=True, fill="both", padx=10, pady=10)

        self.load_favorites()

    def load_favorites(self):
        # Clear existing widgets in the facts frame
        for widget in self.facts_frame.winfo_children():
            widget.destroy()

        self.cursor.execute("SELECT id, fact FROM saved_facts")
        rows = self.cursor.fetchall()

        for id, fact in rows:
            # Fact Label
            fact_label = tk.Label(self.facts_frame, text=fact,
                                   bg=ENTRY_COLOR, fg=TEXT_COLOR,
                                   font=("Segoe UI", 14), wraplength=400,
                                   justify="left", padx=10, pady=5)
            fact_label.grid(row=rows.index((id, fact)), column=0, sticky="ew", padx=5, pady=5)

            # Remove Button
            remove_btn = tk.Button(self.facts_frame, text="Remove",
                                    bg=BUTTON_COLOR, fg="white",
                                    font=("Segoe UI", 12),
                                    command=lambda fact_id=id: self.remove_fact(fact_id),
                                    bd=0, relief=tk.FLAT, cursor="hand2",
                                    highlightthickness=0,
                                    highlightbackground=BG_COLOR, highlightcolor=BG_COLOR,
                                    activebackground=ACCENT_COLOR, activeforeground="white")
            remove_btn.grid(row=rows.index((id, fact)), column=1, sticky="e", padx=5, pady=5)

            # Configure grid to expand properly
            self.facts_frame.grid_columnconfigure(0, weight=1)

    def setup_dashboard_ui(self):
        # Dashboard frame
        self.dashboard_frame = tk.Frame(self.content_frame, bg=BG_COLOR)
        
        # Dashboard Title
        dashboard_title = tk.Label(self.dashboard_frame, text="Dashboard",
                                  bg=BG_COLOR, fg=LABEL_COLOR,
                                  font=("Segoe UI", 20, "bold"))
        dashboard_title.pack(pady=20)
        
        # Fact Count Display
        fact_count = self.cursor.execute("SELECT COUNT(*) FROM saved_facts").fetchone()[0]
        fact_count_label = tk.Label(self.dashboard_frame, text=f"Saved Facts: {fact_count}",
                                    bg=ENTRY_COLOR, fg=TEXT_COLOR,
                                    font=("Segoe UI", 12), padx=10, pady=10)
        fact_count_label.pack(pady=5)
        
        # API Usage Display (Placeholder)
        api_usage_label = tk.Label(self.dashboard_frame, text="API Usage: N/A",
                                   bg=ENTRY_COLOR, fg=TEXT_COLOR,
                                   font=("Segoe UI", 12), padx=10, pady=10)
        api_usage_label.pack(pady=5)

    def load_settings(self):
        try:
            with open('settings.json', 'r') as f:
                settings = json.load(f)
                self.theme_var.set(settings.get('theme', 'dark'))
        except FileNotFoundError:
            pass

    def save_settings(self):
        settings = {
            'theme': self.theme_var.get()
        }
        with open('settings.json', 'w') as f:
            json.dump(settings, f)
        self.apply_theme()

    def apply_theme(self):
        if self.theme_var.get() == 'light':
            self.root.configure(bg="white")
            # Add more theme-specific changes here
        else:
            self.root.configure(bg=BG_COLOR)
            # Add more theme-specific changes here

    def create_rounded_background(self, canvas, width, height):
        points = [
            BORDER_RADIUS, 0,
            width - BORDER_RADIUS, 0,
            width, BORDER_RADIUS,
            width, height - BORDER_RADIUS,
            width - BORDER_RADIUS, height,
            BORDER_RADIUS, height,
            0, height - BORDER_RADIUS,
            0, BORDER_RADIUS
        ]
        canvas.create_polygon(points, fill=ENTRY_COLOR, smooth=True)

    def setup_database(self):
        self.conn = sqlite3.connect('facts.db')
        self.cursor = self.conn.cursor()
        self.cursor.execute('''
        CREATE TABLE IF NOT EXISTS saved_facts
        (id INTEGER PRIMARY KEY, fact TEXT, category TEXT)
        ''')
        self.conn.commit()

    def get_new_fact(self):
        try:
            response = requests.get("https://api.api-ninjas.com/v1/facts?limit=1",
                                  headers={'X-Api-Key': 'YOUR_API_KEY'})
            fact = response.json()[0]['fact']
        except:
            # Fallback facts if API fails
            facts = [
                "The shortest war in history lasted 38 minutes.",
                "Honey never spoils. Archaeologists found 3000-year-old honey in Egypt.",
                "A day on Venus is longer than its year.",
            ]
            fact = random.choice(facts)
        
        self.current_fact = fact
        self.fact_text.config(state="normal")
        self.fact_text.delete(1.0, tk.END)
        self.fact_text.insert(tk.END, fact)
        self.fact_text.config(state="disabled")

    def save_fact(self):
        if self.current_fact:
            self.cursor.execute('INSERT INTO saved_facts (fact, category) VALUES (?, ?)',
                              (self.current_fact, "General"))
            self.conn.commit()
            self.load_favorites()

    def remove_fact(self, fact_id):
        # Remove the fact from the database based on its ID
        self.cursor.execute('DELETE FROM saved_facts WHERE id=?', (fact_id,))
        self.conn.commit()
        self.load_favorites()

    def run(self):
        self.root.mainloop()

    def on_enter(self, e, width, height):
        e.widget.config(width=width, height=height)

    def on_leave(self, e, width, height):
        e.widget.config(width=width, height=height)

if __name__ == "__main__":
    login = LoginScreen()
    login.root.mainloop()
