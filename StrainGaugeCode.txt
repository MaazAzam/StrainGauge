/**
* Name: StrainGauge.c
* Desc: Reads multiple strain gauges and exports readings to a text file
**/

//For printf
#include <stdio.h>

//For the LabJackM Library
#include "LabJackM.h"

//For LabJackM helper functions, such as OpenOrDie, PrintDeviceInfoFromHandle,
//ErrorCheck, etc.
#include "LJM_Utilities.h"

//Basic file operations in order to read and write into a text file
#include <iostream>
#include <fstream>

using namespace std;

int main()
{
	#define CHANNELS 42
	#define READINGS 100

	int err, handle;
	double diff;

	//defining arrays to store the analog input names and values 
	const char * aNames[CHANNELS] = { "AIN2", "AIN3", "AIN120", "AIN121", "AIN122", "AIN123", "AIN124", "AIN125", "AIN126", "AIN127", "AIN52", "AIN53", "AIN54", "AIN55", "AIN56", "AIN57", "AIN58", "AIN59", "AIN60", "AIN61", "AIN76", "AIN77", "AIN78", "AIN79", "AIN80", "AIN81", "AIN82", "AIN83", "AIN84", "AIN85", "AIN100", "AIN101", "AIN102", "AIN103", "AIN104", "AIN105", "AIN106", "AIN107", "AIN108", "AIN109", "AIN0", "AIN96" };
	double aValues[CHANNELS];

	int errorAddress = INITIAL_ERR_ADDRESS; //checking for any errors upon connecting LabJack device

	//Open first found LabJack
	err = LJM_Open(LJM_dtANY, LJM_ctANY, "LJM_idANY", &handle);
	ErrorCheck(err, "LJM_Open");
	PrintDeviceInfoFromHandle(handle);

	//define and open text file
	ofstream file;
	file.open("results.txt");

	for (int n = 0; n < 4; n++)
	{
		file << "\n"; //prints a blank line
	}

	for (int i = 0; i<CHANNELS-2; i++) //looping through names of the analog inputs
	{
		file << aNames[i] << ", "; //printing analog input names to text file
	}

	file << "Vin Reading 1" << ", ";
	file << "Vin Reading 2" << ", ";

	file << "\n"; //print on new line

	for (int x = 0; x<READINGS; x++) //looping through total number of readings needed
	{
		err = LJM_eReadNames(handle, CHANNELS, aNames, aValues, &errorAddress); //reading the analog inputs and recording readings
		ErrorCheckWithAddress(err, errorAddress, "LJM_eReadNames"); //checking for error with analog input addresses

		for (int j = 0; j<CHANNELS; j++) //looping through total number of channels
		{
			file << aValues[j] << ", "; //printing differential output to text file
		}

		file << "\n"; //print on new line
	}

	file.close(); //closing text file

    //closing LabJack device and checking for any potential errors
	err = LJM_Close(handle);
	ErrorCheck(err, "LJM_Close");
	WaitForUserIfWindows();

	return LJME_NOERROR;
}
