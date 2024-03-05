import tkinter as tk
from tkinter import ttk
import requests
import time


class CurrencyApp():
    def __init__(self):
        self.root = tk.Tk()
        self.root.geometry('420x420')
        self.root.title("Currency App")

        # Create an upper frame for the title
        upper_frame = tk.Frame(self.root, background='White')
        upper_frame.pack(fill='both', expand=True)

        # Display the current date on the upper frame
        current_date = time.strftime("%d/%m/%y")
        date_label = ttk.Label(upper_frame, text=f"Date: {current_date}", background='lightblue')
        date_label.grid(row=1, column=0, padx=10, pady=5)

        # Create a custom font for the title
        title_font = ("Arial", 20, "bold")

        self.text = ttk.Label(upper_frame, text='Currency Converter', background='White')
        self.text.grid(row=0, column=0, padx=10, pady=10)

        # Create a bottom frame for the input and output fields
        bottom_frame = tk.Frame(self.root, background='Black')
        bottom_frame.pack(fill='both', expand=True)

        self.from_currency_entry = ttk.Entry(bottom_frame)
        self.from_currency_entry.grid(row=0, column=1, pady=10, padx=10, sticky="W")

        set_from_currency_button = ttk.Button(bottom_frame, text='Set From Currency', command=self.set_from_currency)
        set_from_currency_button.grid(row=0, column=2, pady=10)

        self.to_currency_entry = ttk.Entry(bottom_frame)
        self.to_currency_entry.grid(row=1, column=1, pady=10, padx=10, sticky="W")

        set_to_currency_button = ttk.Button(bottom_frame, text='Set to this Currency', command=self.set_to_currency)
        set_to_currency_button.grid(row=1, column=2, pady=10)

        self.amount_entry = ttk.Entry(bottom_frame)
        self.amount_entry.grid(row=2, column=1, pady=10, padx=10, sticky="W")

        set_amount_button = ttk.Button(bottom_frame, text='Set required Amount', command=self.set_amount)
        set_amount_button.grid(row=2, column=2, pady=10)

        convert_button = ttk.Button(bottom_frame, text='Convert your Money', command=self.convert_currency)
        convert_button.grid(row=3, column=2, pady=10)

        self.converted_amount_entry = ttk.Entry(bottom_frame)
        self.converted_amount_entry.grid(row=4, column=1, pady=10, padx=10, sticky="W")

        self.root.mainloop()

    def set_from_currency(self):
        self.from_currency = self.from_currency_entry.get().upper()

    def set_to_currency(self):
        self.to_currency = self.to_currency_entry.get().upper()

    def set_amount(self):
        self.amount = float(self.amount_entry.get())

    def convert_currency(self):
        url = 'http://api.exchangerate.host/convert'
        access_key = '93ee4c844b8770acfde4519e2e3e5416'

        params = {
            'from': self.from_currency,
            'to': self.to_currency,
            'amount': self.amount,
            'access_key': access_key
        }

        response = requests.get(url, params=params)

        if response.status_code == 200:
            data = response.json()
            if data['success']:
                converted_amount = data['result']
                self.converted_amount_entry.delete(0, 'end')  # Clear the previous result
                self.converted_amount_entry.insert(0, converted_amount)
            else:
                self.converted_amount_entry.delete(0, 'end')
                self.converted_amount_entry.insert(0, f'Error: {data["error"]["info"]}')
        else:
            self.converted_amount_entry.delete(0, 'end')
            self.converted_amount_entry.insert(0, 'Failed to retrieve exchange rate data')


if __name__ == '__main__':
    CurrencyApp()
