Name:Diya Saha
Company:CODTECT IT SOLUTIONS
ID:CT12DS1931
Domain:Cyber Security & Ethical Hacking
Duration:July to September 2024
Mentor:


Project Documentation: Simple Vulnerability Scanner

     Project Overview
The Simple Vulnerability Scanner is a basic tool designed to assess common security vulnerabilities in a target network or website. It checks for open ports, examines server HTTP headers for outdated software versions, and checks for directory listing vulnerabilities. This scanner is designed to be simple, providing an easy way to identify potential security issues.

 Features
Port Scanning:
The tool scans a predefined set of common ports to identify which services are open on the target machine.
Services like FTP, SSH, HTTP, HTTPS, and more are included.
Software Version Check:
The scanner attempts to gather the Server HTTP header from the target's web server.
If a server version is detected, this information can help identify outdated or vulnerable software.
Directory Listing Check:
The tool checks if directory listing is enabled on the web server, which could expose sensitive information about the website structure.
Project Structure
1. Common Ports for Scanning
A predefined set of common ports is listed in the COMMON_PORTS dictionary. Each port is associated with a service (e.g., 21 for FTP, 22 for SSH, etc.).

python
Copy code
COMMON_PORTS = {
    21: "FTP",
    22: "SSH",
    23: "Telnet",
    25: "SMTP",
    80: "HTTP",
    443: "HTTPS",
    3306: "MySQL",
    3389: "RDP",
}
2. Port Scanning Function
The scan_ports function accepts a host (IP address or domain name) and scans each of the predefined ports to see if they are open. It uses Python's socket library to attempt connections and returns a list of open ports along with the associated service.

python
Copy code
def scan_ports(host, port_range=COMMON_PORTS):
    open_ports = []
    for port, service in port_range.items():
        try:
            sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
            sock.settimeout(1)
            result = sock.connect_ex((host, port))
            if result == 0:
                open_ports.append((port, service))
            sock.close()
        except socket.error:
            pass
    return open_ports
3. Software Version Check
The check_software_version function sends an HTTP request to the target server and examines the Server HTTP header. This header typically contains information about the server software version, which can be used to identify outdated or vulnerable versions.

python
Copy code
def check_software_version(url):
    try:
        response = requests.get(url)
        server_header = response.headers.get('Server')
        if server_header:
            print(f"Server header detected: {server_header}")
        else:
            print("No server version found in HTTP headers.")
    except RequestException as e:
        print(f"Error accessing {url}: {e}")
4. Directory Listing Check
The check_directory_listing function sends a request to the target URL and checks if the server returns an "Index of /" response, indicating that directory listing is enabled. Directory listing can expose sensitive files and directories, posing a security risk.

python
Copy code
def check_directory_listing(url):
    try:
        response = requests.get(url)
        if response.status_code == 200 and "Index of /" in response.text:
            print(f"Directory listing enabled at: {url}")
        else:
            print(f"Directory listing seems disabled at: {url}")
    except RequestException as e:
        print(f"Error accessing {url}: {e}")
5. Main Vulnerability Scan
The vulnerability_scan function orchestrates the entire scanning process. It accepts the target host (IP address or domain), scans for open ports, checks the server version, and checks for directory listing vulnerabilities.

python
Copy code
def vulnerability_scan(target):
    print(f"Scanning target: {target}")

    # Check open ports
    print("\nChecking for open ports...")
    open_ports = scan_ports(target)
    if open_ports:
        for port, service in open_ports:
            print(f"Port {port} ({service}) is open.")
    else:
        print("No common ports are open.")

    # Check software version
    print("\nChecking server version...")
    check_software_version(f"http://{target}")

    # Check for directory listing vulnerability
    print("\nChecking for directory listing...")
    check_directory_listing(f"http://{target}")
6. Example Usage
The script prompts the user to enter a target domain or IP address, then runs the vulnerability scan on that target.

python
Copy code
if _name_ == "_main_":
    target = input("Enter target website or IP: ")
    vulnerability_scan(target)
Installation & Requirements
Prerequisites
Python 3.x
The following Python libraries are required:
socket: Pre-installed with Python.
requests: Can be installed via pip:
bash
Copy code
pip install requests
Running the Tool
To run the tool, follow these steps:

Clone or download the script.
Install the necessary dependencies (requests).
Run the script and provide the target website or IP when prompted.
bash
Copy code
python vulnerability_scanner.py
Limitations
The port scanning only checks a limited set of common ports. Expanding the list of ports can provide more comprehensive results.
The server version check relies on the Server HTTP header, which may not always be present or accurate.
Directory listing checks are based on the response containing "Index of /", but other methods might be needed for different web servers.
Future Improvements
Add support for more comprehensive port scanning.
Implement comparison of software versions against known vulnerability databases.
Enhance error handling and logging for better troubleshooting.
Add multi-threading to speed up scanning, especially for large port ranges.
License
This project is licensed under the MIT License. Feel free to modify and use the code as needed.
