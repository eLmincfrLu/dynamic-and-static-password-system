# dynamic-and-static-password-system
A Python program that generates passwords based on dynamic date and time.
# Dynamic Password System

This project is a Python program that generates dynamic passwords based on user-selected date and time components. It provides a user interface using the **Tkinter** library.

## 📋 Features

- **Dynamic Password Generation:**  
  - Users can select the order of components such as day, month, year, hour, and minute.  
  - A password is generated based on the current date and time according to the selected order.

- **User Security:**  
  - The system blocks access after 3 incorrect password attempts.  
  - The blocking duration increases progressively (30 seconds, 1 minute, 1.5 minutes, etc.) with each additional 3 failed attempts.

- **Background Image:**  
  - A visually appealing background image is added to the application window.

## 🛠️ Technologies Used

- **Python 3.x**
- **Tkinter:** For the Graphical User Interface (GUI).
- **Time and Datetime Libraries:** For handling date and time operations.

## 🚀 How to Use

1. When the program starts, select the order of date and time components.
2. Use the system-generated password to log in.
3. If the password is entered incorrectly 3 times consecutively, the system will block access, and the blocking duration will increase incrementally.

## 📂 File Structure
re
- **main.py** - The main program file.
- **assets/** - Contains the background image and other graphical elements.

## 💡 How It Works

1. The program generates a password based on the current time and the user-selected component order.  
2. Password input and validation are handled interactively.  
3. If the password is entered incorrectly, the blocking mechanism activates to enhance security.

## 📜 License

This project is licensed under the [MIT License](LICENSE). Refer to the LICENSE file for more details.

## 🌟 Contact

If you have any questions about the project, feel free to reach out:

- **Email:** elmincfrlu@gmail.com  
- **GitHub:** [MyGitHubProfile](https://github.com/eLmincfrLu))
