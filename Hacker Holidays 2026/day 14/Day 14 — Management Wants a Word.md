
Vera left her laptop behind after checking out early, and IT collected a full KAPE triage before wiping the machine. My job was to investigate the collected artifacts and figure out what Vera had been hiding.

At first, the evidence looked like a collection of unrelated Windows artifacts. However, following the clues from one artifact to another slowly revealed a complete chain that eventually led to the flag.

I started by examining the Windows registry hives collected by KAPE:

SAM  
SYSTEM  
SECURITY

Using `secretsdump.py`, I extracted the available Windows secrets:

python3 secretsdump.py -sam SAM -system SYSTEM -security SECURITY LOCAL

Among the extracted LSA secrets, one entry immediately stood out:

[*] DefaultPassword  
(Unknown User):minivera

This gave me Vera’s Windows password:

minivera

> With the password recovered, I moved on to Vera’s user profile and the DPAPI artifacts. Windows uses DPAPI to protect sensitive information such as browser credentials, so finding the user’s DPAPI masterkey was the next important step.

Inside Vera’s profile I found:

AppData/Roaming/Microsoft/Protect/<SID>/  
c90719ef-5b98-474e-b934-136d606a702a

Using the recovered Windows password, I was able to decrypt the DPAPI masterkey and continue investigating Vera’s Chrome data.

Chrome’s saved credentials were stored in the `Login Data` SQLite database, while the encryption key was stored in the `Local State` file. After recovering the Chrome encryption key through DPAPI, I was able to decrypt the saved credentials.

There was one particularly interesting entry:

URL:  http://bytelotus.thm:8080/  
User: VeraSecretVault  
Pass: Wh4t1sV3raD0inG0nTh1sH0st

The password looked much more interesting than a normal website credential:

Wh4t1sV3raD0inG0nTh1sH0st

It felt like another piece of the puzzle rather than simply a login password.

I continued through Vera’s files and eventually found a suspicious 100 MB file inside her Documents directory:

KAPE/C/Users/vera/Documents/backup

Running `file` against it didn't reveal a normal file type:

backup: data

There was no recognizable file signature, and the contents appeared to be random encrypted data. At this point, the most likely explanation was that `backup` was an encrypted container.

I copied the file first to preserve the original evidence and then attempted to open it as a VeraCrypt/TCRYPT container:

cp ./KAPE/C/Users/vera/Documents/backup ./backup

sudo cryptsetup open --type tcrypt ./backup vera_backup

When prompted for the password, I tried the credential recovered from Chrome:

Wh4t1sV3raD0inG0nTh1sH0st

It worked.

The container opened successfully, and checking it confirmed that it was a TCRYPT volume using AES-XTS:

type:    TCRYPT  
cipher:  aes-xts-plain64

I then checked the filesystem:

sudo blkid /dev/mapper/vera_backup

The result showed:

TYPE="vfat"

I mounted the filesystem read-only:

sudo mkdir -p /mnt/vera_backup  
sudo mount -o ro /dev/mapper/vera_backup /mnt/vera_backup

Inside the mounted volume were several directories, but one immediately caught my attention:

$RECYCLE.BIN  
secret_financial_documents  
System Volume Information

Inside `secret_financial_documents` I found:

important_invoice_byte_lotus.pdf  
transactions_q3.csv

The invoice seemed to be the final piece of evidence. I first tried extracting its text normally:

pdftotext important_invoice_byte_lotus.pdf -

Nothing useful came back.

Since the PDF appeared to contain visual content rather than a normal text layer, I rendered the page as an image:

pdftoppm -png -r 300 important_invoice_byte_lotus.pdf invoice

After opening the rendered image, the hidden message was finally visible.

The first line of the invoice contained the flag:

![](Pasted%20image%200260809231420.png)

Flag: THM{1t_w4s_V3r4_A11_Al0ng?!}