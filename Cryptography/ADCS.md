# 📜 ADCS Active Directory Certificate Services:

In an Active Directory environment, Active Directory Certificate Services (AD CS) is Microsoft’s implementation of public key infrastructure (PKI). This service manages encryption mechanisms, digital certificates, and electronic signatures.

With AD CS, it is possible to enhance the security of exchanges and authentications by associating the identity of a user, machine or service with a corresponding private key. Put simply, AD CS is used to issue, manage and revoke digital certificates.


## Install the AD CS role -PS

 ```cmd
Install-WindowsFeature `
    ADCS-Cert-Authority, `
    ADCS-Cert-Enrollment-Web-Policy, `
    ADCS-Cert-Enrollment-Web-Enrollment `
    -IncludeManagementTools
 ```

## Configure Enterprise Root CA -PS
 ```cmd
# Configure Enterprise Root CA
Install-AdcsCertificationAuthority `
    -CAType EnterpriseRootCA `
    -CACommonName "Contoso Root CA" `
    -CryptoProviderName "RSA#Microsoft Software Key Storage Provider" `
    -KeyLength 4096 `
    -HashAlgorithmName SHA256 `
    -ValidityPeriod Years `
    -ValidityPeriodUnits 10

# Configure Enrollment Policy Web Service
Install-AdcsEnrollmentPolicyWebService `
    -CAConfig "ContosoRootCA\Contoso Root CA" `
    -Force

# Configure Enrollment Web Service & Web Enrollment
Install-AdcsEnrollmentWebService `
    -CAConfig "ContosoRootCA\Contoso Root CA" `
    -Force

Install-AdcsWebEnrollment `
    -CAConfig "ContosoRootCA\Contoso Root CA" `
    -Force
 ```

## Backup Your CA (Highly Recommended) -PS

 ```cmd
# Back up DB + logs
certutil -backupDB C:\CAbackups\CA_DB_Backup

# Back up CA cert + private key to a PFX
certutil -backupKey C:\CAbackups\CA_Key_Backup
 ```
> Never store CA backups on the CA itself. Keep them somewhere safe.

# Creating Certificate Templates & Auto-Enrollment

## Step 1 — Create the certificate templates:
> We’ll duplicate the built-in templates and tune them. Use the GUI for this. Trying to wrestle Certificate Templates into submission with PowerShell is like teaching a cat to fetch. Technically possible, but don’t say I didn’t warn you.

## Create the Machine (Computer) template 
 ```cmd
Create the Machine (Computer) template
Log on to your Certificate Authority.
Run → mmc.exe → File → Add/Remove Snap-in → Certificate Templates → Add → OK.
In the list, right-click Computer → Duplicate Template.
Compatibility: choose values appropriate for your environment (Server 2016 / Windows 10 or later is safe). This controls which options are available; pick something that covers your oldest supported clients.
On the General tab give it a clear name: 8021X-Computer.
On Request Handling:
Leave key purpose as default.
Key size: leave default or set minimum (2048 recommended, 4096 okay). Do not allow private key export (uncheck “Allow private key to be exported”).
On Subject Name:
Select Build from this Active Directory information.
Check Include this information in alternate subject name → DNS name (so SAN contains the machine DNS name). This helps some RADIUS setups and troubleshooting tools.
On Extensions → Application Policies, confirm Client Authentication is present. If not, add it.
On Security:
Add Domain Computers (or the specific computer groups you want to receive the certificates) and grant Enroll and Autoenroll.
Remove unnecessary groups if you are being strict.
Click OK to save the template.
 ```
