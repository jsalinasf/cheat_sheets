# PowerShell useful Comandlets

## Check TCP Port
	Test-NetConnection -ComputerName br01vcc.adistec.com -Port 6180
	Test-NetConnection -ComputerName 200.219.207.133 -Port 6180

## Check Local Open Ports (netstat -an)
	Get-NetTCPConnection | gm
	Get-NetTCPConnection | ? {$_.State -eq "Listen"} (Which ports are open on local computer)
	Get-NetTCPConnection -LocalPort 135 (check if specific local port is open)