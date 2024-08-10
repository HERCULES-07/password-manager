# password-manager
import tkinter as tk
from tkinter import messagebox
import random
import string

def generate_password():

    length = int(entryLength.get())
    use_uppercase = varUppercase.get()
    use_lowercase = varLowercase.get()
    use_digits = varDigits.get()
    use_special = varSpecial.get()

    characters = ""
    if use_uppercase:
        characters += string.ascii_uppercase
    if use_lowercase:
        characters += string.ascii_lowercase
    if use_digits:
        characters += string.digits
    if use_special:
        characters += string.punctuation

    if characters == "":
        messagebox.showerror("Error", "Please select at least one character type!")
        return

    password = ''.join(random.choice(characters) for _ in range(length))

    entryPassword.delete(0, tk.END)
    entryPassword.insert(0, password)

    check_strength(password)


def check_strength(password):
    length_criteria = len(password) >= 8
    uppercase_criteria = any(char.isupper() for char in password)
    lowercase_criteria = any(char.islower() for char in password)
    digits_criteria = any(char.isdigit() for char in password)
    special_criteria = any(char in string.punctuation for char in password)

    if length_criteria and uppercase_criteria and lowercase_criteria and digits_criteria and special_criteria:
        strength = "Strong"
    elif length_criteria and (uppercase_criteria or lowercase_criteria) and (digits_criteria or special_criteria):
        strength = "Medium"
    else:
        strength = "Weak"

    labelStrength.config(text=f"Password Strength: {strength}")

def add():
    username = entryName.get()
    password = entryPassword.get()
    if username and password:
        with open("passwords.txt", 'a') as f:
            f.write(f"{username} {password}\n")
        messagebox.showinfo("Success", "Password added!")
    else:
        messagebox.showerror("Error", "Please enter both fields")


def get():
    username = entryName.get()
    passwords = {}
    try:
        with open("passwords.txt", 'r') as f:
            for k in f:
                i = k.split(' ')
                passwords[i[0]] = i[1].strip()
    except:
        print("ERROR!")

    if passwords:
        mess = "Your passwords:\n"
        for i in passwords:
            if i == username:
                mess += f"Password for {username} is {passwords[i]}\n"
                break
        else:
            mess += "No Such Username Exists!"
        messagebox.showinfo("Passwords", mess)
    else:
        messagebox.showinfo("Passwords", "EMPTY LIST!")

def getlist():
    passwords = {}
    try:
        with open("passwords.txt", 'r') as f:
            for k in f:
                i = k.split(' ')
                passwords[i[0]] = i[1].strip()
    except:
        print("No passwords found!")

    if passwords:
        mess = "List of passwords:\n"
        for name, password in passwords.items():
            mess += f"Password for {name} is {password}\n"
        messagebox.showinfo("Passwords", mess)
    else:
        messagebox.showinfo("Passwords", "Empty List!")

def delete():
    username = entryName.get()
    temp_passwords = []
    try:
        with open("passwords.txt", 'r') as f:
            for k in f:
                i = k.split(' ')
                if i[0] != username:
                    temp_passwords.append(f"{i[0]} {i[1]}")

        with open("passwords.txt", 'w') as f:
            for line in temp_passwords:
                f.write(line)

        messagebox.showinfo("Success", f"User {username} deleted successfully!")
    except Exception as e:
        messagebox.showerror("Error", f"Error deleting user {username}: {e}")


if __name__ == "__main__":
    app = tk.Tk()
    app.geometry("560x400")
    app.title("Password Manager.prabesh kc")

    labelName = tk.Label(app, text="USERNAME:")
    labelName.grid(row=0, column=0, padx=15, pady=15)
    entryName = tk.Entry(app)
    entryName.grid(row=0, column=1, padx=15, pady=15)

    labelPassword = tk.Label(app, text="PASSWORD:")
    labelPassword.grid(row=1, column=0, padx=10, pady=5)
    entryPassword = tk.Entry(app)
    entryPassword.grid(row=1, column=1, padx=10, pady=5)

    labelLength = tk.Label(app, text="Length:")
    labelLength.grid(row=2, column=0, padx=10, pady=5)
    entryLength = tk.Entry(app)
    entryLength.grid(row=2, column=1, padx=10, pady=5)

    varUppercase = tk.BooleanVar()
    checkUppercase = tk.Checkbutton(app, text="Include Uppercase", variable=varUppercase)
    checkUppercase.grid(row=3, column=0, padx=10, pady=5)

    varLowercase = tk.BooleanVar()
    checkLowercase = tk.Checkbutton(app, text="Include Lowercase", variable=varLowercase)
    checkLowercase.grid(row=3, column=1, padx=10, pady=5)

    varDigits = tk.BooleanVar()
    checkDigits = tk.Checkbutton(app, text="Include Digits", variable=varDigits)
    checkDigits.grid(row=4, column=0, padx=10, pady=5)

    varSpecial = tk.BooleanVar()
    checkSpecial = tk.Checkbutton(app, text="Include Special Characters", variable=varSpecial)
    checkSpecial.grid(row=4, column=1, padx=10, pady=5)

    buttonGenerate = tk.Button(app, text="Generate Password", command=generate_password)
    buttonGenerate.grid(row=5, column=0, padx=15, pady=8, sticky="we")

    labelStrength = tk.Label(app, text="Password Strength: ")
    labelStrength.grid(row=5, column=1, padx=10, pady=5)

    buttonAdd = tk.Button(app, text="Add", command=add)
    buttonAdd.grid(row=6, column=0, padx=15, pady=8, sticky="we")

    buttonGet = tk.Button(app, text="Get", command=get)
    buttonGet.grid(row=6, column=1, padx=15, pady=8, sticky="we")

    buttonList = tk.Button(app, text="List", command=getlist)
    buttonList.grid(row=7, column=0, padx=15, pady=8, sticky="we")

    buttonDelete = tk.Button(app, text="Delete", command=delete)
    buttonDelete.grid(row=7, column=1, padx=15, pady=8, sticky="we")

    app.mainloop()
