---
layout: post
title: "M1T2 - Pass2hash - English Version"
date: 2024-04-14 23:25:00 -0400
categories:
    - posts
tags:
    - Master
    - Homework
    - M1
    - script
    - bash
    - en
---

## M1.Task 2. Bash Scripting 

### Professional Master in Offensive Security (OSCP) (03/26/2024 - 03/31/2025)

- **Student:** Nicolas Sadofschi | gen0ne | [https://xargs.cat](https://xargs.cat)

# Task Statement

This task consists of two parts:

In the first part, you need to create a script that will receive two arguments:

    - The first argument is the path to the hashes file (available on the campus). The hashes file contains a series of hashes. The hashes are in sha256 format (generated without line breaks with echo).
    - The second argument is a wordlist file (you can reduce it or create your own).

The idea is to read plain text words from your wordlist (typical passwords), hash them using SHA256 (without line breaks), and compare them with the hashes in the hashes file.

Objectives: 

    - Find at least one word in the hashes file.
    - More importantly: create the script that automates this entire search.

# Part 1 
```bash
#!/bin/bash

#####################################################################
# Script: pass2hash.sh
# Description: The purpose of this script is to provide two files, one of hashes and another of passwords, in order to search for matches between the provided passwords and hashes.
#
# Master: Professional Master in Offensive Security (OSCP)
# Module: M1
# Task: Task 2 - Bash Scripting; First part
#
# Author: Nicolás Damián Sadofschi | gen0ne | xargs.cat
# Date: 14/04/2024
# Version: 1.0
#
# Change history:
# 14/04/2024 22:15 Added this header.
# 14/04/2024 22:30 Corrected spelling errors.
#
#####################################################################

echo "Task:"
echo "The first argument is the path to the hashes file (available on the campus). The hashes file contains a series of hashes. The hashes are in sha256 format (generated without line breaks with echo)."
echo "The second argument is a wordlist file (you can reduce it or create your own)."
echo "The idea is to read plain text words from your wordlist (typical passwords), hash them using SHA256 (without line breaks), and compare them with the words in the hashes file."
echo ""
echo "Objectives:"
echo "* Find at least one word in the hashes file."
echo "* More importantly: create the script that automates this entire search."
echo "======================================================================"
echo ""
echo "Instructions: ./script <hashes> <wordlist>"
echo ""
echo "Note: The script will print any matches found."
echo ""
echo "Press Enter to continue..."
read

if [ $# -eq 2 ] # Evaluate if two arguments are passed
then
        hashes=$1 # The first argument is the hashes file
        passwords=$2 # The second argument is the wordlist file
        
        if [ -e $hashes ] # Check if the first argument is an existing file
        then
                :
        else
                echo "The argument $hashes is not a valid file"
                echo "Usage: $0 <hashes.txt> <wordlist.txt>"
                exit
        fi

        if [ -e $passwords ] # Check if the second argument is an existing file
        then
                match=0
                nomatch=0
                while read line # Read and perform actions line by line.
                do
                pass2hash=$(echo -n $line | sha256sum | cut -f1 -d' ') # For each line, calculate the SHA256 hash, clean the output, and assign it to a variable for comparison.
                        if grep -q $pass2hash $hashes # Grep the previous variable, if exists, print the result.
                        then
                                echo "Result found: The password '$line' whose hash is $pass2hash is present in $1"
                                match=$(($match +1))
                        else
                                nomatch=$(($nomatch +1))
                        fi
                done < $passwords


        else
                echo "The argument $passwords is not a valid file"
                echo "Usage: $0 <hashes.txt> <wordlist.txt>"
                exit
        fi

else
        echo "Usage: $0 <hashes.txt> <wordlist.txt>"
        exit
fi

echo "The number of passwords that match the hash is: $match"
echo "The number of passwords that do NOT match the hash is: $nomatch"
```