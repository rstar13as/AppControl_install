# Carbon Black App Control Install
# Example Microsoft Vagrantfile - https://github.com/microsoft/azure_arc/blob/main/azure_arc_servers_jumpstart/local/vagrant/windows/Vagrantfile
# Carbon Black App Control Pre-requesits script - https://github.com/rstar13as/AppControl_install/blob/main/AppControl_Preres.ps1
# Operating System Architecture Service Pack Additional Notes/Requirements
# Windows Server 2012 R2 x64 Use Latest If virtual, HVM only
# Windows Server 2016 x64 Use Latest If virtual, HVM only
# Windows Server 2019 x64 Use Latest If virtual, HVM only
# Windows Server 2022 x64 Use Latest If virtual, HVM only
# Vagrant - Window Server - https://app.vagrantup.com/StefanScherer

Vagrant.configure("2") do |config|
  config.vm.box = "StefanScherer/windows_2022" # This image bluescreens on first boot, but, I don't care.
  config.vm.provider "vmware_desktop" do |v|
    v.gui = true
  end

  # https://docs.vmware.com/en/VMware-Carbon-Black-App-Control/services/cb-ac-announcements/GUID-63037C41-25EA-4BD1-A53A-EABAA2F87711.html
  # 8.10.0 Server Download Link
  # IMPORTANT: Before using the download link, make sure you have logged into the Carbon Black User Exchange (UEX).
  # Files put inside C:\Users\vagrant\Documents
  config.vm.provision "file", source: "Servers_CB App Control Server_8.10.0.485.zip", destination: "Servers_CB App Control Server_8.10.0.485.zip"

  config.vm.provision "shell", inline: <<-SHELL, privileged: true
    # Set Execution Policy, enable TLS 1.2, and install Chocolatey
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
    choco feature enable -n allowGlobalConfirmation
    choco install vscode -y

    # Invoke-WebRequest -Uri https://raw.githubusercontent.com/rstar13as/AppControl_install/main/AppControl_Preres.ps1 -OutFile .\AppControl_Preres.ps1; .\AppControl_Preres.ps1

    #Open "Windows PowerShell ISE" as Administrator
    Set-ExecutionPolicy Bypass -Scope Process

    #Disable Windows Defender 
    Set-MpPreference -DisableRealtimeMonitoring $true
    #Uninstall Windows Defender
    Remove-WindowsFeature Windows-Defender

    #Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    #Enable IIS options
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-WebServerRole
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-WebServer
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-CommonHttpFeatures
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-HttpErrors
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-HttpRedirect
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-ApplicationDevelopment
    Enable-WindowsOptionalFeature -online -FeatureName NetFx4Extended-ASPNET45
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-NetFxExtensibility45
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-HealthAndDiagnostics
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-HttpLogging
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-LoggingLibraries
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-RequestMonitor
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-HttpTracing
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-Security
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-RequestFiltering
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-WebServerManagementTools
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-ManagementConsole

    # OER: https://docs.vmware.com/en/VMware-Carbon-Black-App-Control/8.10/cb-ac-oer.pdf
    # You must disable Basic Authentication and Windows Authentication so that the App Control Server handles authentication:
    Disable-WindowsOptionalFeature -Online -FeatureName IIS-BasicAuthentication
    Disable-WindowsOptionalFeature -Online -FeatureName IIS-WindowsAuthentication

    Enable-WindowsOptionalFeature -Online -FeatureName IIS-StaticContent
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-DefaultDocument
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-ISAPIExtensions
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-ISAPIFilter
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-ASPNET45
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-CGI
    Enable-WindowsOptionalFeature -Online -FeatureName IIS-ManagementScriptingTools


    # Install SQL Server Express 2019.

    function Install-SQLServerExpress2019 {
        Write-Host "Downloading SQL Server Express 2019..."
        $Path = $env:TEMP
        $Installer = "SQL2019-SSEI-Expr.exe"
        $URL = "https://go.microsoft.com/fwlink/?linkid=866658"
        Invoke-WebRequest $URL -OutFile $Path\$Installer

        Write-Host "Installing SQL Server Express..."
        Start-Process -FilePath $Path\$Installer -Args "/ACTION=INSTALL /IACCEPTSQLSERVERLICENSETERMS /QUIET" -Verb RunAs -Wait
        Remove-Item $Path\$Installer
    }

    Install-SQLServerExpress2019


    # https://docs.vmware.com/en/VMware-Carbon-Black-App-Control/services/cb-ac-announcements/GUID-63037C41-25EA-4BD1-A53A-EABAA2F87711.html
    # 8.10.0 Server Download Link
    # IMPORTANT: Before using the download link, make sure you have logged into the Carbon Black User Exchange (UEX).
    # I just copied the link from the corresponding download link, I am not sure if this link expires, so you may need to update this link as required, or download it to local first.
    # Invoke-WebRequest -Uri "" -OutFile .\8.10.0.485.zip
  
    # Expand-Archive -Path "C:\Users\vagrant\Documents\Servers_CB App Control Server_8.10.0.485.zip" -DestinationPath "C:\Users\vagrant\Documents"
    # "C:\Users\vagrant\Documents\ParityServerSetup.exe"
    # This doesnt work yet
  
    SHELL
end
