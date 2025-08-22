+++
date = "Wed, 21 Aug 2013 19:05:00 +0000"
title = "Code Sample - BASH - FTP Server Management Script - IvansTool"
description = "I've created some scripts for managing an FTP server (for allowing non command line people a way to remotely manage the service via SSH) While posting this script I see places that could be improved, simplified, and commented. &nbsp;I may or may not come back and dress it up a bit..."
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
externalLink = "https://nerdler.ivanlawrence.com/2013/08/code-sample-bash-ftp-server-management.html"
series = [ ]
+++

# Code Sample - BASH - FTP Server Management Script - IvansTool

I've created some scripts for managing an FTP server (for allowing non command line people a way to remotely manage the service via SSH)  
  
While posting this script I see places that could be improved, simplified, and commented.  I may or may not come back and dress it up a bit... time permitting.  
  
This script is called "IvansTool":  
  

#!/bin/bash
#IvansTool
#
#------------------------------------------
#
#       Ivan Lawrence
#       written: Jan 26, 2010
#
#------------------------------------------
#
#   >>FUNCTIONS<<
function if0 {
if \[ -z $1 \]
then
 echo "No empty variables!"
 echo "$2 was empty!"
 exit 0
#else
# echo "$2 = $1"
fi
}
function pause {
read -p "Press the \[ENTER\] or \[RETURN\] key to continue or CTR+C to cancel..."
}
#
# See what Productions have a mounted volume
function listProd {
GetProd=$(df -H | grep /data/ | awk -F" " '{print $5}' | awk -F"/" '{print $3}' | sort -d)
select ProdName in $GetProd
do
if \[\[ -n $ProdName \]\]
then
 break
else
 echo "Select a number..."
fi
done
}
#
# See which users have been created
function listUser {
GetUser=$(grep /data/ /etc/passwd | awk -F":" '{print $1}' | sort -d)
select username in $GetUser
do
if \[\[ -n $username \]\]
then
 break
else
 echo "Select a number..."
fi
done
}
#
# create a password from a limited "alphabet" excluding confusing chars
function passwordGen {
unset PASS
MATRIX="123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnpqrstuvwxyz!@#$%&\*?-\_=+)(}{\]\[><"
LENGTH="8"
n=1
while \[ "$n" -le "$LENGTH" \]
do
        RealRand=$(dd if=/dev/urandom count=1 2> /dev/null | cksum | cut -f1 -d" ")
        PASS="$PASS${MATRIX:$(($RealRand%${#MATRIX})):1}"
 #echo "$PASS"
        let n+=1
done
password=$PASS
}
#
# create a password from a limited "alphabet" excluding confusing chars and in a human friendly pattern
function custPassGen {
unset PASS
MATRIX="123456789ABCDEFGHJKLMNPQRSTUVWXYZabcdefghijkmnpqrstuvwxyz!@#$%&\*?-\_=+)(}{\]\[><"
Cap="ABCDEFGHJKLMNPQRSTUVWXYZ"
Low="abcdefghijkmnpqrstuvwxyz"
Punc="!@#$%&\*?-\_=+)(}{\]\[><"
Num="123456789"
LENGTH="9"
n=1
while \[ $n -le "$LENGTH" \]
do
 RealRand=$(dd if=/dev/urandom count=1 2> /dev/null | cksum | cut -f1 -d" ")
#       PASS="$PASS${MATRIX:$(($RealRand%${#MATRIX})):1}"
 if \[\[ (($n > 3)) && (($n < 7)) \]\]
 then
  PASS="$PASS${Cap:$(($RealRand%${#Cap})):1}"
#  echo "$PASS"
 fi
 if \[\[ (($n < 3)) \]\]
 then
  PASS="$PASS${Low:$(($RealRand%${#Low})):1}"
#  echo "$PASS"
 fi
 if \[\[ (($n == 3)) || (($n == 7)) \]\]
 then
  PASS="$PASS${Punc:$(($RealRand%${#Punc})):1}"
#  echo "$PASS"
 fi
 if \[\[ (($n > 7)) \]\]
 then
  PASS="$PASS${Num:$(($RealRand%${#Num})):1}"
#  echo "$PASS"
 fi
 let n+=1
# echo $n
done
password=$PASS
}
function emailRootAdminemail {
# experimental: I have not tested this loop for sending email to root per account!
emailDir=Scripts
address=root
if \[ -f $emailDir/ftp\_email\_body \]
then
 sh $emailDir/ftp\_email\_body $1 $2 > $emailDir/ftpemail
 mail $address -s "FTP account created: $1" < $emailDir/ftpemail
 rm $emailDir/ftpemail
else
 echo "can't find $emailDir/ftp\_email\_body"
fi
}
function passGen {
echo "Either a totally random password:"
passwordGen
echo $password
echo "Or a formulaic password:"
echo "(lower x2, special x1, capital x3, special x1, integer x2)"
custPassGen
echo $password
}
function diskUsage {
echo "Display usage for"
echo "A - All"
echo "P - a Production"
echo "L - Logins in a Production"
read usageChoice
case $usageChoice in
 A | a ) echo "Dir  Size  Used  Avail  Use% "
 df -h | grep /data/ | awk -F" " '{print $5,$1,$2,$3,$4}' | sort -d
 ;;
 P | p ) listProd
 echo "Dir  Size  Used  Avail  Use% "
 df -h /data/$ProdName | grep /data/ | awk -F" " '{print $5,$1,$2,$3,$4}'
 ;;
 L | l ) listProd
 echo "\*note: This could take a long time depending on the volume size"
 du -ch --max-depth=1 /data/$ProdName | awk -F"/" '{print $1,$4}' | grep -v lost+found
 ;;
 \* ) echo "Enter your selection." ;;
esac
}
function rawSpace {
PEtotal=$(pvdisplay /dev/sdb1 | grep Total | awk '{print $3}')
PEfree=$(pvdisplay /dev/sdb1 | grep Free | awk -F" " '{print $3}')
PEsize=$(pvdisplay /dev/sdb1 | grep "PE Size" | awk -F" " '{print $4}')
GBfree=$(((($PEfree\*32)-30627)/1024))
GBtotal=$(((($PEtotal\*32)-30627)/1024))
echo "$GBfree GB free out of $GBtotal GB"
}
function usersFromCSV {
echo "If you used scp to get the file here I can move the file from your home dir to root's"
GetSSHuserhome=$(grep /home/ /etc/passwd | grep nologin -v | awk -F":" '{print $1}' | sort -d)
select SSHuserhome in $GetSSHuserhome root
do      
if \[\[ -n $SSHuserhome \]\]
then
        break
else
        echo "Select a number..."
fi
done
if \[\[ $SSHuserhome == "root" \]\]
then
 UserHomeCSV=$(find /$SSHuserhome -name \*.csv)
else
 UserHomeCSV=$(find /home/$SSHuserhome -name \*.csv)
 if \[\[ -f $UserHomeCSV \]\]
 then
  read -p "Move $UserHomeCSV to /root for use? (y/n) " moveCSV
  if \[\[ $moveCSV == "y" || $moveCSV == "Y" \]\]
  then
   mv $UserHomeCSV /root/
  else
   echo "Try again."
   exit
  fi
 else
  echo "No CSV file found in $SSHuserhome Try Again"
  exit
 fi
fi
echo "CSV must have no headers and be formatted: ProductionName, size (# in GB only), username, password, (additional info ignored)"
echo "Make sure there are no empty fields, empty lines, or unwanted spaces in lines!"
echo "\*NOTE\* You will enter VIM to edit the file, to remove lines navigate to the line to be deleted and strike the letter d twice."
echo "To exit VIM and continue strike ESC the type :q to quit, if changes have been made strike ESC then type :wq."
FindCSV=$(find /root -name \*.csv)
read -p "I've found $FindCSV, would you like to use it? \[y/n\] " useResult
if \[\[ $useResult == "y" || $userResult == "Y" \]\]
then
 vim $FindCSV
 CSVFILE=$FindCSV
else
 read -p "Path to CSV for user creation:" CSVFILE
fi
if0 $CSVFILE "CSVFILE"
read -p "Would you like a form email for each account sent to the admin account? \[y/n\] " sendRootEmail
echo "About to use File: $CSVFILE to create volumes and user accounts"
pause
if \[ -f $CSVFILE \]
then
 exec < $CSVFILE
 while read line
 do
  TODAY=$(date +"%Y%m%d")
  TIME=$(date +"%H%M")
  ProductionName=$(echo $line | awk -F, '{print $1}')
  size=$(echo $line |awk -F, '{print $2}')
  username=$(echo $line | awk -F, '{print $3}')
  password=$(echo $line | awk -F, '{print $4}')
  if0 $ProductionName "ProductionName"
  if0 $size "size"
  if0 $username "username"
  if0 $password "password"
  echo "File entry = $ProductionName, $size G, $username, $password"
  if \[ -d /data/$ProductionName \]
  then
   CHKVolGroup=$(lvdisplay | grep $ProductionName | awk -F" " '{print $3}')
   echo "Logical Volume already exists! ($CHKVolGroup)"
  else
   echo "Creating the volume for $ProductionName"
   lvcreate -L $size"g" -n $ProductionName VolGroupData 
   mkfs.ext3 /dev/mapper/VolGroupData-$ProductionName
   mkdir /data/$ProductionName
   mount /dev/VolGroupData/$ProductionName /data/$ProductionName
   cp /etc/fstab /etc/fstab.backups/fstab.$TODAY$TIME
   echo "/dev/VolGroupData/$ProductionName /data/$ProductionName ext3 defaults 0 0" >> /etc/fstab
   echo "Created volume: $ProductionName = $size G"
  fi
  id -un $username
  if \[ $? == 0 \]
  then
   echo "User $username already exists!"
   echo "$password" | passwd $username --stdin
   echo "The password for $username has been updated to $password"
   # experimental: I have not tested this loop for sending email to root per account!
   if \[\[ $sendRootEmail == "y" || $sendRootEmail == "Y" \]\]
   then
    emailRootAdminemail $username $password
   fi
  else
   useradd -m -d /data/$ProductionName/$username $username
   echo "$password" | passwd $username --stdin
   echo "User created: $username / $password"
   # experimental: I have not tested this loop for sending email to root per account!
   if \[\[ $sendRootEmail == "y" || $sendRootEmail == "Y" \]\]
   then
    emailRootAdminemail $username $password
   fi
  fi
 #end while loop
 done
 if \[\[ $sendRootEmail == "y" || $sendRootEmail == "Y" \]\]
 then
  echo "Accounts have been created and emails sent"
 else
  echo "Accounts have been added but no email sent"
  echo "NOTE: if you had answered Y to the erlier email question but are seeing this there was a problem"
 fi
 read -p "Delete $CSVFILE? (y/n) " CSVFileDEL
 echo "$CSVFILE and $CSVFileDEL"
 if \[\[ $CSVFileDEL == "y" || $CSVFileDEL == "Y" \]\]
 then
  rm $CSVFILE
 else
  echo "\*WARNING\*: CSV file ($CSVFILE) still exists!" 
 fi
else
 echo "file not found"
 exit 0
fi
}
function userAdd {
echo "Add a Single User to an existing Production Partition"
echo "Select the Production:"
listProd
if0 $ProdName "ProdName"
read -p "Username: " username
if0 $username "username"
custPassGen
read -p "Use password $password \[y\] or create your own? \[(c)reate\]" genpass
if0 $genpass "genpass"
if \[ $genpass == "c" \] || \[ $genpass == "create" \]
then
 read -p "Password: " password
 if0 $password "password"
fi
read -p "Add $username to $ProdName using password $password ? \[y/n\]: " adduser
if0 $adduser "adduser"
if \[ $adduser != "y" \]
then
 echo "No user added"
 exit 0
else
if \[ -d /data/$ProdName \]
then
 id -un $username
 if \[ $? == 0 \]
 then
  echo "User $username already exists!"
  exit 0
 else
  useradd -m -d /data/$ProdName/$username $username
  echo "$password" | passwd $username --stdin
  echo "User created: $username / $password"
  emailDir=Scripts
  address=dl-bur-serveradmin@starz.com
  if \[ -f $emailDir/ftp\_email\_body \]
  then
   sh $emailDir/ftp\_email\_body $username $password > $emailDir/ftpemail
   mail $address -s "FTP account created: $username" < $emailDir/ftpemail
   rm $emailDir/ftpemail
  else
   echo "can't find $emailDir/ftp\_email\_body"
  fi
 fi
 else
  echo "Production does not exist!"
  exit 0
 fi
fi
}
function userProdAdd {
TODAY=$(date +"%Y%m%d")
TIME=$(date +"%H%M")
echo "Add a new Production Partition and user."
echo "ProductionName (all one word)"
echo "Capitalize the 1st letter of each word:"
echo "(ex: PostProd, IT, or Simpsons) "
read ProductionName
if0 $ProductionName "ProductionName"
read -p "Usable Gigs for $ProductionName: " size
if0 $size "size"
read -p "Username: " username
if0 $username "username"
#read -p "Password: " password
#if0 $password "password"
custPassGen
read -p "Use password $password \[y\] or create your own? \[(c)reate\]" genpass
if0 $genpass "genpass"
if \[ $genpass == "c" \] || \[ $genpass == "create" \]
then
 read -p "Password: " password
 if0 $password "password"
fi
#Check to see if the Production has a DIR
if \[ -d /data/$ProductionName \]
then
 CHKVolGroup=$(lvdisplay | grep $ProductionName | awk -F" " '{print $3}')
 echo "Logical Volume / Production already exists! ($CHKVolGroup)"
 echo "Use a different Production name or choose 'Add a user to a Production'"
 exit 0
else
 echo "create the volume"
 #create the Logical Volume
 lvcreate -L $size"g" -n $ProductionName VolGroupData 
 mkfs.ext3 /dev/mapper/VolGroupData-$ProductionName
 mkdir /data/$ProductionName
 mount /dev/VolGroupData/$ProductionName /data/$ProductionName
 cp /etc/fstab /etc/fstab.backups/fstab.$TODAY$TIME
 echo "/dev/VolGroupData/$ProductionName /data/$ProductionName ext3 defaults 0 0" >> /etc/fstab
 echo "Created volume: $ProductionName = $size G"
fi
id -un $username
if \[ $? == 0 \]
then
 echo "User $username already exists!"
 echo "Try Again!"
 exit 0
else
 useradd -m -d /data/$ProductionName/$username $username
 echo "$password" | passwd $username --stdin
 echo "User created: $username / $password"
 emailDir=Scripts
 address=dl-bur-serveradmin@starz.com
 if \[ -f $emailDir/ftp\_email\_body \]
 then
  sh $emailDir/ftp\_email\_body $username $password > $emailDir/ftpemail
  mail $address -s "FTP account created: $username" < $emailDir/ftpemail
  rm $emailDir/ftpemail
 else
  echo "can't find $emailDir/ftp\_email\_body"
 fi
fi
}
function userDel {
echo "This will remove the FTP share and it's data"
listUser
usernameHome=$(grep $username /etc/passwd | cut -d ':' -f6)
id -un $username
if \[ $? == 0 \]
then
 echo $username"'s home dir is " $usernameHome
 echo "If you want to also remove the Production partition then abort and choose that option."
 read -p "Continue removing only this user and it's data? \[y/n\]: " continue
 if \[ $continue != "y" \]
 then
  echo "Nothing removed"
 else
  userdel -r $username
  echo "User $username has been deleted!"
 fi
else
 echo "User $username does not exist!"
 exit 0
fi
}
function userProdDel {
TODAY=$(date +"%Y%m%d")
TIME=$(date +"%H%M")
echo "Which production will you be destroying today?"
echo "Remember, this can not be undone!"
listProd
if0 $ProdName "ProdName"
ProdNameCK=$(grep "/data/$ProdName" /etc/fstab)
if \[ -z "$ProdNameCK" \]
then
 echo "Production does not exists!"
 exit 0
else
 echo "The production has an entry in /etc/fstab of:"
 grep $ProdName /etc/fstab | cut -d ' ' -f1,2
 ProdNameFSTAB1=$(grep $ProdName /etc/fstab | cut -d ' ' -f1)
 ProdNameFSTAB2=$(grep $ProdName /etc/fstab | cut -d ' ' -f2)
 echo "The usernames associated with that Volume are:"
 ProdNamePASSWD=$(grep $ProdName /etc/passwd | cut -d ':' -f1)
 for u in $ProdNamePASSWD
 do
  echo "$u"
 done
 echo "Are you sure you want to continue?"
 echo "\*\*\* If there is no entry in /etc/fstab for $ProdName then do not continue!"
 echo "Check your spelling and try again (use the command df -H to list all volumes in use)"
 echo "Continuing will remove all the data in the FTP share(s) under $ProdName."
 read -p "Delete $ProdNameFSTAB2 \*\*note: Once done you can not go back! \[y/n\] " answer1
 if \[ $answer1 != "y" \]
 then
  exit
 else
  echo "Now deleteing $ProdNameFSTAB2 from the server"
  read -p "Remove users: $ProdNamePASSWD \[y/n\]: " userdel
  if \[ $userdel != "y" \]
  then
   echo "user not deleted"
  else
   for u in $ProdNamePASSWD
   do
   userdel -r $u
   echo "User $u has been deleted!"
   done
  fi
  cp /etc/fstab /etc/fstab.backups/fstab.$TODAY$TIME
  grep $ProdNameFSTAB2 /etc/fstab >> /etc/fstab.removed
  echo $TODAY-$TIME >> /etc/fstab.removed
  grep -v $ProdNameFSTAB2 /etc/fstab > /etc/fstab.tmp
  cp /etc/fstab.tmp /etc/fstab
  umount $ProdNameFSTAB2
  lvremove $ProdNameFSTAB1
  read -p "Remove the DIR (rm -r $ProdNameFSTAB2) \[y/n\]: " dirrm
  if \[ $dirrm != "y" \]
  then
   echo "dir not deleted"
  else
   rm -r $ProdNameFSTAB2
  fi
  echo "\*\*\*\*\*\*\*\* DONE \*\*\*\*\*\*\*\*"
 fi
fi
}
function volExtend {
echo "\*Warning - Use caution, there is no checking so be sure your math is correct!\*"
echo "Which production needs more space?"
listProd
LVPath=$(lvdisplay | grep $ProdName | awk '{print $3}')
CurrentSize=$(df -h | grep /data/$ProdName | awk '{print $1}' | sed s/.$//)
echo "You've selected $ProdName which is currently $CurrentSize G in size."
read -p "Please specify the new total (in Gigs)? " NewSize
echo "About to expand $ProdName (currently $CurrentSize G) to $NewSize G"
pause
#if \[\[ $CurrentSize -lt $NewSize \]\]
#then
 lvextend -L $NewSize"g" $LVPath
 resize2fs $LVPath
#else
# echo "Error: Cannot srink the volume, the New size must be larger then the current size!"
#fi
}
function passReset {
custPassGen
read -p "Use password $password \[y\] or create your own? \[(c)reate\]" genpass
if \[\[ $genpass == c \]\]
then
 read password
else
 echo "using $password"
fi
echo "List by production or user"
echo "P - Production"
echo "U - User"
read resetChoice
case $resetChoice in
 P | p ) listProd
 ProdNamePASSWD=$(grep $ProdName /etc/passwd | cut -d ':' -f1)
 echo "Reset all the users passwords?"
 for u in $ProdNamePASSWD
 do
  echo "Reset $u to $password"
 done
 pause
 for u in $ProdNamePASSWD
 do
  echo "$password" | passwd $u --stdin
  echo "User $u password = $password"
 done
 ;;
 U | u ) listUser
 read -p "reset $username password to $password \[y/n\]" ResetPass
 if \[\[ $ResetPass == y \]\]
 then
  echo "$password" | passwd $username --stdin
  echo "user / pass is now $username / $password"
 else
  exit
 fi
 ;;
 \* ) echo "Enter your selection." ;;
esac
}
function emailtest {
echo "enter the username/password you would like included in the FTP Account form email"
read -p "username:" emailtestuser
read -p "password:" emailtestpass
emailRootAdminemail $emailtestuser $emailtestpass
}
#Be sure it's running as root!
if \[ "$(whoami)" != "root" \]
then
        echo "Error: You must be ROOT to add users!"
        exit 1
else
# selection=
# until \[ "$selection" = "0" \]; do
  echo ""
  echo "#==> Make your choice! <==#"
  echo "1 - Display Production/disk usage"
  echo "2 - Display free raw disk space"
  echo "------------------------------"
  echo "3 - New Production and User"
  echo "4 - Add a User to a Production"
  echo "------------------------------"
  echo "5 - Remove a Production and User(s)"
  echo "6 - Remove a single User"
  echo "------------------------------"
  echo "7 - Add users or reset all passwords via CSV file"
  echo "8 - Remove users via CSV file"
  echo "------------------------------"
  echo "9 - Enlarge a production volume"
  echo "10 - Password Creator"
  echo "11 - Password Reset"
  echo "------------------------------"
  echo "12 - Generate FTP user/pass Admin email"
  echo ""
  echo "q - exit program"
  echo ""
  read -p "Enter selection: " selection
  case $selection in
   1 ) diskUsage ;;
   2 ) rawSpace ;;
   3 ) userProdAdd ;;
   4 ) userAdd ;;
   5 ) userProdDel ;;
   6 ) userDel ;;
   7 ) usersFromCSV ;;
   8 ) echo "under construction" ;;
   9 ) volExtend ;;
   10 ) passGen ;;
   11 ) passReset ;;
   12 ) emailtest ;;
   q ) exit ;;
   \* ) echo "You're that stupid huh... Try Again!"
  esac
# done
fi

End