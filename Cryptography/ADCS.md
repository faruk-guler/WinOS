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

## Creating Certificate Templates & Auto-Enrollment
