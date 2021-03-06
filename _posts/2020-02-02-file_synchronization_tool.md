--- 
layout: post 
title: "File Synchronization Tool" 
date: 2020-02-02 16:05:26 
author: Dewey Mao 
categories: Python 
--- 
"Incremental" synchronization file via network shared folder.

```
# auto file synchronization 2020-02-02

import os
import hashlib

# create all file directory(exclude root) and md5 of path
def creatFileMD5Dict(root,path,listPathDir,dictFileMD5):
	for name in os.listdir(path):
		if name=="$RECYCLE.BIN" or name=="System Volume Information": 
			continue; 
		childPath = path+os.sep+name
		if os.path.isdir(childPath):
			listPathDir.append(childPath[len(root):])
			creatFileMD5Dict(root,childPath,listPathDir,dictFileMD5)
		else:
			with open(childPath,'rb') as file:
				md5 = hashlib.md5(file.read()).hexdigest()
				if md5 not in dictFileMD5:
					dictFileMD5[childPath[len(root):]] = md5

# remove all of path(include file)				
def removeDir(path):
	for name in os.listdir(path):
		childPath = path+os.sep+name
		if os.path.isdir(childPath):
			removeDir(childPath)
			os.rmdir(childPath)
			print('[del-dir] : ',childPath)
		else:
			os.remove(childPath)
			print('[del-fil] : ',childPath)

# copy fileSource to fileTarget
def copyFile(fileSource,fileTarget):
	with open(fileSource,'rb') as fs:
		with open(fileTarget,'wb') as ft:
			ft.write(fs.read())
	print('[add-fil] : ',fileTarget)

# synchronize directory
def syncDir(pathSource,pathTarget,listSourceDir,listTargetDir):
	for sDir in listSourceDir:
		if sDir not in listTargetDir:
			if os.path.isdir(pathSource+sDir):
				removeDir(pathSource+sDir)
				os.rmdir(pathSource+sDir)
				print('[del-dir] : ',pathSource+sDir)
	for tDir in listTargetDir:
		if tDir not in listSourceDir:
			os.mkdir(pathSource+tDir)
			print('[add-dir] : ',pathSource+tDir)
			
# synchronize file
def syncFile(pathSource,pathTarget,dictSourceFileMD5,dictTargetFileMD5):
		for sFile in dictSourceFileMD5:
			if sFile not in dictTargetFileMD5:
				if os.path.isfile(pathSource+sFile):
					os.remove(pathSource+sFile)
					print('[del-fil] : ',pathSource+sFile)
		for tFile in dictTargetFileMD5:
			if tFile in dictSourceFileMD5:
				if dictSourceFileMD5[tFile] != dictTargetFileMD5[tFile]:
					if os.path.isfile(pathSource+tFile):
						os.remove(pathSource+tFile)
						print('[del-fil] : ',pathSource+tFile)
						copyFile(pathTarget+tFile,pathSource+tFile)
			else:
				copyFile(pathTarget+tFile,pathSource+tFile)

# main entrance
def autoFileSync():
	listSourceDir = [] 			# source dir exclude path_source
	listTargetDir = []			# target dir exclude path_target
	dictSourceFileMD5 = {}		# source file path exclude path_source - file md5
	dictTargetFileMD5 = {}		# target file path exclude path_target - file md5
	
	print('******************************** AUTO FILE SYNCHRONIZATION ********************************')
	print('FUNCTION: SYNCHRONIZE TARGET PATH TO SOURCE PATH.')
	path_source = input('Please input your source path(F:\\\\source): ')
	path_target = input('Please input your target path(//192.168.0.101/target): ')
	if os.path.isdir(path_source):
		if os.path.isdir(path_target):
			creatFileMD5Dict(path_source,path_source,listSourceDir,dictSourceFileMD5)
			print('Source Directory Count: ',len(listSourceDir))
			print('Source File Count: ',len(dictSourceFileMD5))
			creatFileMD5Dict(path_target,path_target,listTargetDir,dictTargetFileMD5)
			print('Target Directory Count: ',len(listTargetDir))
			print('Target File Count: ',len(dictTargetFileMD5))
			syncDir(path_source,path_target,listSourceDir,listTargetDir)
			syncFile(path_source,path_target,dictSourceFileMD5,dictTargetFileMD5)
		else:
			print('[error] : target path error!')
	else:
		print('[error] : source path error!')
	print('******************************** AUTO FILE SYNCHRONIZATION ********************************')

autoFileSync()
input()

```

### Reference 
- <a href="https://github.com/deweymao/SyncFile" target="_blank"> deweymao-SyncFile </a> 
