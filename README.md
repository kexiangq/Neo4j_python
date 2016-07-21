# Neo4j_python
###############################################################
### Script to process csv files                             ###
### Usage: python3 l.py [input file path] [output file path]###
###############################################################

import csv
import sys
import math
import time

loops = 2000000
###### Process the Bus
inputFileBus = open("Bus_raw.csv","r")
outputFileBus = open("b.csv","w+", newline='')

inputBus = csv.reader(inputFileBus)
outputBus = csv.writer(outputFileBus)

headerBus = next(inputBus)
headerBus[0] = ('BUSID:ID')
outputBus.writerow(headerBus)

for i in range(loops):
	inputFileBus = open("Bus_raw.csv","r")
	inputBus = csv.reader(inputFileBus)
	header = next(inputBus)
	for row in inputBus:
		BusNumBus = int(row[0])+i*10
		row[0] = BusNumBus
		outputBus.writerow(row)	

###### Process the transmission Line	
	
inputFileTL = open("TL_raw.csv","r")
outputFileTL = open("tl.csv","w+", newline='')
outputFileSwitch = open("switch.csv","w+", newline='')
outputFileRel = open("rel.csv","w+", newline='')

inputTL = csv.reader(inputFileTL)
outputTL = csv.writer(outputFileTL)
outputSwitch = csv.writer(outputFileSwitch)
outputRel = csv.writer(outputFileRel)


headerTL = next(inputTL)
headerTL.append("LineID:ID")
headerTL.append("FromID")
headerTL.append("ToID")
outputTL.writerow(headerTL)
outputSwitch.writerow(('Name','BUSID','LineID','SwitchID:ID','Status'))
outputRel.writerow((':START_ID',':END_ID',':TYPE'))


for i in range(loops):

	inputFileTL = open("TL_raw.csv","r")
	inputTL = csv.reader(inputFileTL)
	header = next(inputTL)
	
	if i == 0:
	
		inputFileTL = open("TL_raw.csv","r")
		inputTL = csv.reader(inputFileTL)
		header = next(inputTL)
		for row in inputTL:
			from_id = pow(10,12)*3+int(row[0])*pow(10,6)+int(row[1])             
			row.append(str(from_id))
			row.append(str(from_id))
			switch_from_id = int(from_id)+pow(10,12)
			to_id = pow(10,12)*3+int(row[1])*pow(10,6)+int(row[0])             
			row.append(str(to_id))
			switch_to_id = int(to_id)+pow(10,12)
			
			outputTL.writerow(row)
			outputSwitch.writerow(('Switch',row[0],from_id,switch_from_id,'on'))
			outputSwitch.writerow(('Switch',row[1],to_id,switch_to_id,'on'))
			outputRel.writerow((row[0],switch_from_id,'connect'))
			outputRel.writerow((switch_from_id,from_id,'connect'))
			outputRel.writerow((from_id,switch_to_id,'connect'))
			outputRel.writerow((switch_to_id,row[1],'connect'))
			
	else :	
		inputFileTL = open("TL_raw.csv","r")
		inputTL = csv.reader(inputFileTL)
		header = next(inputTL)	
		for row in inputTL:
			BusNumTL = int(row[0])+i*10
			row[0] = BusNumTL
			BusNum1TL = int(row[1])+i*10
			row[1] = BusNum1TL 
			
			from_id = pow(10,12)*3+int(row[0])*pow(10,6)+int(row[1])             
			row.append(str(from_id))
			row.append(str(from_id))
			switch_from_id = int(from_id)+pow(10,12)
			to_id = pow(10,12)*3+int(row[1])*pow(10,6)+int(row[0])             
			row.append(str(to_id))
			switch_to_id = int(to_id)+pow(10,12)
			
			outputTL.writerow(row)
			outputSwitch.writerow(('Switch',row[0],from_id,switch_from_id,'on'))
			outputSwitch.writerow(('Switch',row[1],to_id,switch_to_id,'on'))
			outputRel.writerow((row[0],switch_from_id,'connect'))
			outputRel.writerow((switch_from_id,from_id,'connect'))
			outputRel.writerow((from_id,switch_to_id,'connect'))
			outputRel.writerow((switch_to_id,row[1],'connect'))
		
		ConnectRow = row 
		ConnectRow[1] = int(1)+(i)*10
		ConnectRow[0] = int(6)+(i-1)*10
		ConnectRow[9] = pow(10,12)*3+int(ConnectRow[0])*pow(10,6)+int(ConnectRow[1])
		ConnectRow[10] = pow(10,12)*3+int(ConnectRow[0])*pow(10,6)+int(ConnectRow[1])
		ConnectRow[11] = pow(10,12)*3+int(ConnectRow[1])*pow(10,6)+int(ConnectRow[0])
		switch_from_id_conct = int(ConnectRow[9])+ pow(10,12)
		switch_to_id_conct = int(ConnectRow[11])+ pow(10,12)
		
		outputTL.writerow(ConnectRow)
		outputSwitch.writerow(('Switch',ConnectRow[0],ConnectRow[9],switch_from_id_conct,'on'))
		outputSwitch.writerow(('Switch',ConnectRow[1],ConnectRow[11],switch_to_id_conct,'on'))
		outputRel.writerow((ConnectRow[0],switch_from_id_conct,'connect'))
		outputRel.writerow((switch_from_id_conct,ConnectRow[9],'connect'))
		outputRel.writerow((ConnectRow[9],switch_to_id_conct,'connect'))
		outputRel.writerow((switch_to_id_conct,ConnectRow[1],'connect'))	
