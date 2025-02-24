# Python Streamlit in NGINX
---

## **1. Update Ubuntu & Install Dependencies**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install python3-pip python3-venv nginx -y
```

---

## **2. Set Up Your Streamlit App**
### **Navigate to your project directory and create a virtual environment**
```bash
mkdir streamlit_app && cd streamlit_app
python3 -m venv venv
source venv/bin/activate
```

### **Install Streamlit**
```bash
pip install streamlit
```

### **Create Your Streamlit App**
```bash
nano app.py
```
Paste the following example code:
```python
import streamlit as st

st.title("Hello, Streamlit!")
st.write("This is a deployed Streamlit app.")
```

---

## **3. Test Your App Locally**
Run:
```bash
streamlit run app.py --server.port 8501 --server.address 0.0.0.0
```
Then visit **http://your-server-ip:8501** in your browser.

---

## **4. Create a Systemd Service for Streamlit**
Exit the app (`Ctrl + C`) and create a systemd service:
```bash
sudo nano /etc/systemd/system/streamlit.service
```
Add the following:
```ini
[Unit]
Description=Streamlit App
After=network.target

[Service]
User=ubuntu
Group=ubuntu
WorkingDirectory=/home/ubuntu/streamlit_app
ExecStart=/home/ubuntu/streamlit_app/venv/bin/streamlit run /home/ubuntu/streamlit_app/app.py --server.port 8501 --server.address 0.0.0.0
Restart=always

[Install]
WantedBy=multi-user.target
```
**Note**: Replace `ubuntu` with your username if different.

Reload systemd and start the service:
```bash
sudo systemctl daemon-reload
sudo systemctl start streamlit
sudo systemctl enable streamlit
```

Check if it’s running:
```bash
sudo systemctl status streamlit
```

---

## **5. Set Up NGINX as a Reverse Proxy**
Create a new NGINX config:
```bash
sudo nano /etc/nginx/sites-available/streamlit
```
Add:
```nginx
server {
    listen 80;
    server_name your_server_ip;

    location / {
        proxy_pass http://127.0.0.1:8501;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
Save and enable the config:
```bash
sudo ln -s /etc/nginx/sites-available/streamlit /etc/nginx/sites-enabled/
```

Test NGINX:
```bash
sudo nginx -t
```
Restart NGINX:
```bash
sudo systemctl restart nginx
sudo systemctl enable nginx
```

---

## **6. Allow Firewall Rules**
```bash
sudo ufw allow 'Nginx Full'
```

---

## **7. (Optional) Secure with SSL**
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d your_domain
sudo certbot renew --dry-run
```

---

## **8. Test Deployment**
Visit `http://your-server-ip` in your browser. You should see your Streamlit app running! 🚀  

Let me know if you run into any issues! 😊
