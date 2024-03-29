
**Make by DolPhinSon**

# Table of contents


1. [Script](#script)
2. [Giải thích SSL và testcookie](#giai-thich)



# Script


## Script backup mysql and sync to GG Drive

_Bức tranh toàn cảnh: Sử dụng python kết hợp cùng package subprocess để thực hiện các cmd._

Vấn đề chưa giải quyêt: 
- Với việc sử dụng ``rclone`` client phải thực hiện ``rclone config`` đê có file rclone.conf để thực hiện lấy access key kết nối đến GG Driver. Nếu không sẽ throw error và thoát script.

- Việc xác định hệ điêu hành cũng là quan trọng để có thê chạy script một cách thuận lợi nhất.

Demo 

+ TH1: Với ``rclone`` được cài đặt và config sãn sàng

![backupmysql](/Script_task/Image/script_backup_sync_complete.png)

![backupmysql](/Script_task/Image/ggdrive_sync.png)


> Trong trường hợp này, có thế dùng ``crontab`` để lập lịch cho script chạy trong khoảng thời gian chỉ định (1 lần mỗi ngày,...)

+ TH2: Với ``rclone`` chưa được cài đặt lẫn config

![backupmysql](/Script_task/Image/mysqlbackupwithnorclone.png)


```python
#Chỉnh sửa các biến/path (mysql, rclone config)cho phù hợp từ line 80 
#Run by python/python3 script_backupmysql.py

#Import lib/package
import os
import subprocess
import sys
import platform
#check package

#Declare func

def check_install_app():

    if get_os_kernel() == "Linux":
        print("This script will work smoothly on Linux.")

    print("Stating check package...")
    #Get and install rclone about 5 minutes
    try:
        subprocess.run("rclone version", shell=True, check=True)
    except subprocess.CalledProcessError:
        subprocess.run("curl https://rclone.org/install.sh | sudo bash", shell=True)


## Func backup
def backup_mysql_db(username, password, output_dir, current_datetime):

    #check folder backupmysql exist
    if not os.path.exists(output_dir):
        os.makedirs(output_dir)
    print(output_dir)

    #get datetime to distinguish
    backup_file = os.path.join(output_dir, f'backup_{current_datetime}.sql')
    
    # all-databases use with root permission
    command = f"mysqldump -u {username} -p {password} --all-databases > {backup_file}"
    subprocess.run(command, shell=True)

    print("Backup done!!")

# Target: 3 file recently (sort) and rclone sync to gg drive 
# With standared format of backup, can ez sort file backup and get latest to sync

def sync(backup_dir, num_files,config_file, current_remote):

    list_files = os.listdir(backup_dir)

    #This is filter and get files with suitable ext (.sql), sort files by time 
    backup_files = [file for file in list_files if file.startswith('backup_') and file.endswith('.sql')]
    sorted_files = sorted(backup_files, key=lambda x: os.path.getmtime(os.path.join(backup_dir, x)), reverse=True)
    require_files = sorted_files[:num_files]
    
    print("Latest backup files:")
    for file in require_files:
        print(file)

    #Sync with rclone
    #này trường hợp là đã có file config của rclone rồi, xác suất xảy ra hi hữu :))
    if os.path.exists(config_file):
        print("Sync starting...")
        for file in require_files:
            file_path = os.path.join(backup_dir, file)
            command = f"rclone sync {file_path} {current_remote}:backup_folder"
            try:
                subprocess.run(command, shell=True, check=True)
            except subprocess.CalledProcessError as e:
                print(f"Sync fails but backup completed database , please check config of rclone: {e}")
                sys.exit(1)

    # else :
        #Chạy lệnh rclone config xong cấu hinh các file như thủ công (vì nếu muốn vào gg drive thì trong file config cần có access key)
        #File cấu hình cần access key nên không thể hoàn chỉnh từ ở mỗi client.
        #Yuppp hướng là vậy, sẽ suy nghĩ thêm :<


def main():

    #Define var which connect to mysql
    #prefer: password on file script
    username = "root"
    password = "password"

    # Proccessing relatives path and set path in current folder
    ## set up for func backup_mysql_db
    script_dir = os.path.dirname(os.path.abspath(__file__))
    output_dir = os.path.join(script_dir, "backupmysql")
    current_datetime = subprocess.run('date +"%Y-%m-%d_%H-%M-%S"', shell=True, capture_output=True, text=True)
    current_datetime = current_datetime.stdout.strip() #Formating datetime 
    

    ##set up paramater for func sync
    #path dùng đẻ kiểm tra tồn tại rclone.conf 
    config_dir = os.path.expanduser('~/.config/rclone')
    config_file = os.path.join(config_dir, 'rclone.conf')
    current_remote = "drive"
    num_files = 3

    #Call func
    backup_mysql_db(username, password, output_dir, current_datetime)
    sync(output_dir,num_files,config_file,current_remote)
    print("Everything OK!")

if __name__ == "__main__":
    check_install_app()
    main()

```


## Script generate CSR + private key

_Bức tranh toàn cảnh: Sử dụng python kết hợp cùng package subprocess để thực hiện các cmd._

Xử lý: 
- Tạo private_key với ``openssl genpkey -algorithm RSA -out <file_path> -outform PEM -aes256 -pass pass: <password>``

- Tạo csr với ``openssl req -new -key /path/private_key.pem -out csr_path -subj "/CN=common_name"``

- Tạo ra các file ngay trong folder chạy script -> exception quăng ra khi đã tồn tại trùng file.

![generateCSR](/Script_task/Image/exception_script_genCSR.png)

Minh họa tạo thành công

![generateCSR](/Script_task/Image/script_genCSR.png)

```python

#Include lib/package

import subprocess
import os
import platform
import sys
#Define func

def get_os_kernel():
    return platform.system()

def generate_csr_and_private_key(common_name, output_dir, password_decrypt_PR, file_name_PR, file_name_CSR):

    # Generate private key
    
    private_key_path = f"{output_dir}/{file_name_PR}.pem"
    csr_path = f"{output_dir}/{file_name_CSR}.pem"

    #Exception when exist
    if os.path.exists(private_key_path) or os.path.exists(csr_path):
        print(f" File '{file_path}' has been detected, please solve this problem to continue (rename or delete)")
        sys.exit(1)

    #openssl genpkey -algorithm RSA -out /home/private_key.pem -outform PEM -aes256 -pass pass:
    subprocess.run(["openssl", "genpkey", "-algorithm", "RSA", "-out", private_key_path, "-outform", "PEM", "-aes256", "-pass", f"pass:{password_decrypt_PR}"], check=True)

    # Generate CSR
    
    subprocess.run(["openssl", "req", "-new", "-key", private_key_path, "-out", csr_path, "-subj", f"/CN={common_name}"], check=True)

    return private_key_path, csr_path


def main():
    #common_name = tên miền
    common_name = "test.com"
    file_name_PR = "private_key"
    file_name_CSR = "csr"
    password_decrypt_PR = "password"
    output_dir = os.path.dirname(os.path.abspath(__file__))
    
    if get_os_kernel() == "Linux":
        print("This script will work smoothly on Linux.")

    private_key_path, csr_path = generate_csr_and_private_key(common_name,output_dir, password_decrypt_PR)
    print(private_key_path)
    print(csr_path)
    print(platform.system())


if __name__ == "__main__":
    main()

```



## Script compile nginx với  module testcookie



_Bức tranh toàn cảnh: Sử dụng python kết hợp cùng package subprocess để thực hiện các cmd._

Luồng script xử lý: uninstall current nginx -> download modules -> configure nginx (make install/modules) -> create nginx.service -> add nginx.conf -> start nginx

Script thực hiện được:

- Gỡ hoàn toàn nginx bản cũ và cài đặt nginx từ source theo phiên bản chỉ định cùng với việc cài đặt các dependencies cần thiết.

- Tải module và thực hiện --add-dynamic-module theo chỉ định (dựa vào việc cung cấp link repo module và tên module).

- Thực hiện việc tạo file nginx config với module testcookie.

Sau khi chạy script hiển thị các dòng thông báo này là ok!

![nginx_module](/Script_task/Image/script_nginx_run.png)

Minh họa testcookie hoạt động

![generateCSR](/Script_task/Image/redirect_google.png)


Hướng tối ưu hóa:

- Dùng script theo dạng command-line utility thay vì 1 click sẽ tận dụng hết các chức năng và thân thiện hơn (vd: 1. Uninstall current Nginx; 2. Install nginx; 3.Add modules ;4.Import nginx.conf)
- Kiểm tra tính sẵn sàng: các port chiếm dụng hiện tại đê khởi động nginx mượt hơn.

```python
#Chỉnh sửa tham số trong def main() line 200+

#Include lib/package
import subprocess
import os
import platform
import sys
#Define func 

## Func check nginx version and down it

#Giả định là nginx cài đặt từ nguồn

def install_nginx_source(version,path_module):

#install Dependencies
    try:
        dependencies = [
            "gcc", "make", "build-essential", "libpcre3", "libpcre3-dev",
            "zlib1g", "zlib1g-dev", "libssl-dev", "libgd-dev",
            "libxml2", "libxml2-dev", "uuid-dev"
        ]
        subprocess.run(["apt", "install", "-y"] + dependencies, check=True)
        print("Installed dependencies")
    except subprocess.CalledProcessError as e:
        print(f"Error: {e}")
    
#check nginx downloading yet.
    if os.path.exists(f"nginx-{version}.tar.gz"):
        print("Nginx downloaded and will use it!")
        subprocess.run([f"tar", "-xvf", f"nginx-{version}.tar.gz"])
    else:
        #Install nginx 1.2.2
        print("To start downloading Nginx v1.2.2....")
        subprocess.run(["wget", f"http://nginx.org/download/nginx-{version}.tar.gz"], check=True)
        #Extract nginx
        subprocess.run(["tar", "-xvf", f"nginx-{version}.tar.gz"])
        
    try: 
        #chdir into nginx folder
        current_dir = os.path.dirname(__file__)
        nginx_source_dir = os.path.join(current_dir, f"nginx-{version}")

        # Di chuyển vào thư mục chứa mã nguồn của Nginx
        os.chdir(nginx_source_dir)
    except (OSError, FileNotFoundError) as e:
        print("Không thể di chuyển vào thư mục chứa mã nguồn của Nginx:", e)
            
    current_directory = os.getcwd()
    print("Thư mục làm việc hiện tại:", current_directory)


#.configure nginx
    print(f"To start configuring Nginx v{version}....")
    add_module = f"--add-dynamic-module={path_module}"
    configure_command = [
    "./configure",
    "--prefix=/etc/nginx",
    "--pid-path=/var/run/nginx.pid",
    "--conf-path=/etc/nginx/nginx.conf",
    "--sbin-path=/usr/sbin/nginx",
    "--user=nginx",
    "--group=nginx",
    "--with-http_ssl_module",add_module]
    try:
        subprocess.run(configure_command, check=True)
        subprocess.run(["make", "install"])
        subprocess.run(["make", "modules"])
        # #module tạo trong objs
        # so_file_modules = current_directory + "/objs" + module_name
    except subprocess.CalledProcessError as e:
        print(f"Error: {e}")
        print("Please check for required library or path modules")
        sys.exit(1)
    print("Configuring and making OK!")



#Thuc hien viec check port mac dinh cua nginx co bi chiem boi apache hay cac ung dung nao khong
    #something


def processing_nginx(version, port, server_name,path_module):

    flag = 1
    try:
        subprocess.run("nginx -v", shell=True, check=True)
    except subprocess.CalledProcessError:
        #install nginx 1.22 from source
        install_nginx_source(version,path_module)
        flag = 0

    if flag == 1:   
        #uninstall current nginx
        print("To start uninstalling currently Nginx....")
        subprocess.run("service nginx stop", shell=True)
        subprocess.run("rm -rf /etc/nginx /etc/default/nginx /usr/sbin/nginx* /usr/local/nginx /var/run/nginx.pid /var/log/nginx", shell=True)
        install_nginx_source(version, path_module)

    #Add service # vi /lib/systemd/system/nginx.service
    if os.path.exists("/lib/systemd/system/nginx.service"):
        print("For smoothly, script will delete the old nginx.service")
        subprocess.run("rm -f /lib/systemd/system/nginx.service", shell=True)
    nginx_service_content = """
                [Unit]
                Description=A high performance web server and a reverse proxy server
                Documentation=man:nginx(8)
                After=network.target nss-lookup.target

                [Service]
                Type=forking
                PIDFile=/run/nginx.pid
                ExecStartPre=/usr/sbin/nginx -t -q -g 'daemon on; master_process on;'
                ExecStart=/usr/sbin/nginx -g 'daemon on; master_process on;'
                ExecReload=/usr/sbin/nginx -g 'daemon on; master_process on;' -s reload
                ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /run/nginx.pid
                TimeoutStopSec=4
                KillMode=mixed

                [Install]
                WantedBy=multi-user.target
                """
    nginx_service_path = "/lib/systemd/system/nginx.service"

    with open(nginx_service_path, "w") as f:
        f.write(nginx_service_content)

    subprocess.run("systemctl daemon-reload", shell=True)

#Edit file nginx.conf
    edit_nginx_config()

    subprocess.run("service nginx start", shell=True)

    subprocess.run("service nginx status", shell=True)



# Func add module for nginx

def add_module(module_link,module_name):
    try: 
        subprocess.run(f"git clone {module_link}", shell=True, check = True)
        print("Downloaded module")
    except subprocess.CalledProcessError:
        print("Please check the validity of the module link")
    
    current_dir = os.path.dirname(__file__)
    module_dir = os.path.join(current_dir, module_name)
    
    return module_dir

def edit_nginx_config():
    nginx_conf_path = "/etc/nginx/nginx.conf"

    subprocess.run("truncate -s 0 /etc/nginx/nginx.conf", shell=True)

    nginx_config = f"""
    #user  nobody;
    worker_processes  1;
    load_module modules/ngx_http_testcookie_access_module.so;

    error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;

    #pid        logs/nginx.pid;

    events {{
        worker_connections  1024;
    }}

    http {{
        include       mime.types;
        #default config, module disabled
        testcookie off;

        #setting cookie name
        testcookie_name dophison;

        #setting session key
        testcookie_session $remote_addr;

        #setting argument name
        testcookie_arg ckattempt;

        #setting maximum number of cookie setting attempts
        testcookie_max_attempts 3;
        #setting p3p policy
        testcookie_p3p 'CP="CUR ADM OUR NOR STA NID", policyref="/w3c/p3p.xml"';

        #setting fallback url

        testcookie_fallback http://google.com;


        #configuring whitelist
        testcookie_whitelist {{
            8.8.8.8/32;
    }}

    default_type  application/octet-stream;


    sendfile        on;

    keepalive_timeout  65;
    
    server {{
        listen       {port};
        server_name  {server_name};


        error_page   500 502 503 504  /50x.html;
        location = /50x.html {{
            root   html;
        }}

         location / {{
            #enable module for specific location
            testcookie on;
        }}

    }}
    }}
    """

    with open(nginx_conf_path, "w") as f:
        f.write(nginx_config)

    print("Added nginx configuration to nginx.conf successfully.")


#Define main()

def main():
    
    #Check nginx 
    version = "1.22.1"
    #Xu ly trong truong hop can set file config cua nginx
    port = "80"
    server_name = "localhost"
    module_link = "https://github.com/kyprizel/testcookie-nginx-module.git"
    module_name = "testcookie-nginx-module"

    path_module = add_module(module_link, module_name)

    processing_nginx(version, port, server_name, path_module)

if __name__ == "__main__":
    main()

```


# Giải thích
_Giải thích quá trình request SSL từ lúc gửi file csr đến CA và lúc xác thực là chủ sở hữu domain._


File CSR(Certificate Signing Request) chứa thông tin về trang web và khóa công khai, được gửi đến CA 

Quá trình xác thực chủ sở hữu domain được thực hiện trước khi CA cung cấp chứng chỉ SSL bao gồm việc gửi email xác minh đến các địa chỉ email được liệt kê trong thông tin liên hệ WHOIS của domain hoặc thông qua việc tạo một tệp mã hóa đặc biệt.

Sau khi xác minh thì CA sẽ phát hành chứng chỉ SSL theo các bước như hình. 


![SSL](/Script_task/Image/2024-03-26_17-21.png)

_Giải thích được mô hình testcookie hoạt động như thế nào._

Script compile nginx với  module testcookie. Confirm testcookie hoạt động. Giải thích được mô hình testcookie hoạt động như thế nào.

Mô hinh testcookie phát hiện xem một yêu cầu HTTP có phải là của một máy tính thực sự hay của một botnets hoạt động  

Thêm cookie vào yêu cầu: Mô hình Testcookie sẽ thêm một cookie vào phản hồi HTTP mà máy chủ web gửi đến trình duyệt của người dùng. 

Yêu cầu trả lời với cookie: Khi trình duyệt của người dùng nhận được phản hồi từ máy chủ web, nó sẽ gửi lại yêu cầu mới đến máy chủ, bao gồm cả cookie mà máy chủ đã gửi.

Phân tích yêu cầu: Máy chủ web sẽ phân tích yêu cầu mới này và kiểm tra xem cookie đã được gửi lại hay không.

Nếu yêu cầu không bao gồm cookie, hoặc nếu cookie không hợp lệ, máy chủ web có thể kết luận rằng đó có thể là một cuộc tấn công từ một botnets, vì người dùng thực sự sẽ gửi lại cookie đó.

![SSL](/Script_task/Image/simple_set-cookie.png)





