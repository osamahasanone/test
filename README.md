1. Install AWS Command Line Interface (CLI):
	 * Linux:	
    ```
		curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
		unzip awscliv2.zip
		sudo ./aws/install
    ```
	 * Windows:
		```
		msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
		```	
    To make sure you have aws successfully installed:	  
    ```
	  aws --version
    ``` 
    For more information about AWS CLI installation, please check this [link](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
2. Install saml2aws:
   you need this CLI tool to login and generate AWS temporary credintials using a SAML IDP.
	 * Linux:
     1. Download and install [Homebrew](https://docs.brew.sh/Homebrew-on-Linux).
        
        ```
			 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
			  ```
        
     2. Add Homebrew to your PATH and to your bash shell profile script.
        
        ```
			 test -d ~/.linuxbrew && eval "$(~/.linuxbrew/bin/brew shellenv)"
			 test -d /home/linuxbrew/.linuxbrew && eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
			 test -r ~/.bash_profile && echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >>~/.bash_profile
			 echo "eval \"\$($(brew --prefix)/bin/brew shellenv)\"" >>~/.profile
		    ```
        
     3. Install saml2aws:  
        ```
			  brew install saml2aws
	      ```    
  * Windows:
		1. Install chocolatey https://chocolatey.org/install:
			```
			powershell -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))"
	    ```
		2. Install saml2aws:
      ```
			choco install saml2aws
      ```
  For more information about saml2aws installation, please check this [link](https://github.com/Versent/saml2aws).
	
To make sure you have saml2aws successfully installed:	
	
	saml2aws --version
	

3. Configure a new IDP account (replace firstname and lastname with yours):


saml2aws configure \
  --idp-account tier-sandbox \
  --idp-provider=Okta \
  --username=firstname.lastname@tier.app \
  --url=https://okta.tier-services.io/home/amazon_aws/0oa2vbiihzhrlCRur0i7/272 \
  --skip-prompt \
  --mfa=OKTA \
  --role=arn:aws:iam::199745669981:role/TM-cross-account-admin \
  --session-duration=7200 \
  --profile=TM-cross-account-admin \
  --region=eu-central-1

on Windows, replace '\' with ';'


4. Set defaul AWS region as environment variable:
	Linux:

		export AWS_DEFAULT_REGION="eu-central-1"
	
	Windows:
	
		$env:AWS_DEFAULT_REGION="eu-central-1"

5. login to AWS:

	saml2aws login --idp-account tier-sandbox

	A verification code will be sent to your okta verify mobile application.

6. set AWS default profile:
	
	Linux:

		export AWS_PROFILE=TM-cross-account-admin
 	
	Windows:

		$env:AWS_PROFILE="TM-cross-account-admin"

7. Add some environment variables related to AWS account:
	
	Linux:

		eval $(saml2aws script --idp-account="tier-sandbox")
	
	Windows:

		$Commands = $(saml2aws script --idp-account="tier-sandbox" --shell=powershell)
		foreach ($Command in $Commands){
    			if ($Command -ne ""){
        			Invoke-Expression $Command
    			}
		}



8. Set CODEARTIFACT_TOKEN environment variable:
	
	Linux:

		export CODEARTIFACT_TOKEN="$(aws codeartifact get-authorization-token --domain tier --domain-owner 373437620866 --query authorizationToken --output text)"
	
	Windows Powershell ISE:
	
		$env:CODEARTIFACT_TOKEN="$(aws codeartifact get-authorization-token --domain tier --domain-owner 373437620866 --query authorizationToken --output text)"	
	
9. git clone https://github.com/TierMobility/pricing-services.git

10. docker-compose build --build-arg CODEARTIFACT_TOKEN=$CODEARTIFACT_TOKEN

11. docker-compose up
