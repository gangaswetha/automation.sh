# automation.sh
name="swetha"
s3_bucket="upgrad_swetha"

# update the 
sudo apt-get update 

# Check if apache2 is installed
if [[ apache2 != $(dpkg --get-selections apache2 | awk '{print $1}') ]]; then
	#statements
	apt install apache2 -y
fi

# Ensures that apache2 service is running
running=$(systemctl status apache2 | grep active | awk '{print $3}' | tr -d '()')
if [[ running != ${running} ]]; then
	#statements
	systemctl start apache2
fi

# Ensures apache2 Service is enabled 
enabled=$(systemctl is-enabled apache2 | grep "enabled")
if [[ enabled != ${enabled} ]]; then
	#statements
	systemctl enable apache2
fi

# Creating file name
timestamp=$(date '+%d%m%Y-%H%M%S')

# Create tar archive of apache2 access and error logs
cd /var/log/apache2
tar -cf /tmp/${name}-httpd-logs-${timestamp}.tar *.log

# copy logs to s3 bucket
if [[ -f /tmp/${name}-httpd-logs-${timestamp}.tar ]]; then
	#statements
	aws s3 cp /tmp/${name}-httpd-logs-${timestamp}.tar s3://${s3_bucket}/${name}-httpd-logs-${timestamp}.tar
  fi
