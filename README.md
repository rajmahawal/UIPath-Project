# UIPath-Project
Find My Artist Concert

Hi Humans!

Raj here, I like UIPath and Robotics(that's what I'm made of!).

Objective of this document is to provide complete walkthrough of the automation solution implementation for Finding Artist Concert Details problem.

Problem: Given a Band or Artist Name, the system should respond accordingly as per the below guidelines.
1. If the band is playing in Sydney soon then the date of the performance is reported.
2.If the band is playing somewhere else in the world, the costs of the flights and accommodation will be to go and see their band or artist play in their next performance (leaving 2 days before and returning 2 days after the concert) is reported.
3.If the band isn't playing, an artist that they might like along with flight and accommodation costs

Solution Walkthrough
FindArtistsConcert holds all the solution artifacts.

FindArtistsConcert/ FindArtistsConcertBusinessProcess.xaml is the root solution file that depicts the whole solution.

The whole solution functional complexity is modeled as 5 states and the various transitions among them as outlined below.
State	Function	Transitions
Init	Read config from excel into in memory dictionary, Prompt for BandName entry, Validate BandName	Invalid Band Name -- Init 
Valid Band Name --SearchConcert
Error -- Exception State
SearchConcert	Search in Concerts website to find Concert and records its details for the given BandName	Concert in Syd – DisplayResult
Concert Not in Syd –DisplayResult
Concert Unavailable – FindAlternativeBand
Error -- Exception State
FindAlternativeBand	Finds Alternative BandNames and Invokes concert search for recording the concert details for this Alternative Band 	Alternative Band Not In Sydney – DisplayResult
Alternative Band In Syd -- DisplayResult
No Alternative Bands—Init
Error -- Exception State
DisplayResult	Display all cases results and Closes all opened applications	This is the final state
Exception State	Display all exceptions and Closes all opened applications	This is the final state
		
Init state responsibility is divided into the following workflow implementations
FindArtistsConcert/ GetConfigData.xaml
FindArtistsConcert/ GetValidBandName.xaml
FindArtistsConcert/ KillAllProcesses.xaml

SearchConcert state responsibility is divided into the following workflow implementations
FindArtistsConcert/ SearchConcert.xaml
FindArtistsConcert/ GetFlightAndHotelDetails.xaml

FindAlternativeBand state responsibility is divided into the following workflow implementations
Inbuilt login and 
FindArtistsConcert/ SearchConcert.xaml
FindArtistsConcert/ GetFlightAndHotelDetails.xaml

Exception state and DisplayResult state uses the following workflow implementation
FindArtistsConcert/ KillAllProcesses.xaml

Individual workflow file functional Details:
1.KillAllProcesses.xaml
•	Finds all running chrome processes and closes them.
2.GetConfigData.xaml
•	Start Log Message
•	Initialize Config Dictionary variable
•	Loop each sheet in Config\Data.xlsx and read each setting and store as key value pairs in Config Dictionary
•	End Log Message
3.GetValidBandName.xaml
•	Start Log message
•	Open BandName input dialog
•	BandName Log message
•	End Log message
4.SearchConcert.xaml
•	Start Log Message
•	Open Concert Search URL in Chrome browser
•	Maximize window
•	Type BandName into Concert search field and press enter
•	Check if No results content displayed
•	If No results assign the ConsertCount variable to 0
•	If Yes go to ResultsSequence
•	Scan the BandName and ConsertCount information from the first listing
•	Then Click on it to go to details page
•	In the details page extract the concert details string.
•	Check the string to see if the concert is in Sydney.
•	If in Sydney, Capture the Location and Concert date
•	If not in Sydney, Capture the Location and Concert date and invoke the flight and hotel details workflow
  5.GetFlightAndHotelDetails.xaml
•	Start Log Message
•	Open Flight Search URL in Chrome browser
•	Maximize window
•	Type the search location, start date and end date into respective text fields and click search
•	Capture the result and store in FlightDetails variable
•	Open Hotel Search URL in Chrome browser
•	Maximize window
•	Type the search location, start date and end date into respective text and calendar fields and click search
•	Click guest popular filter button
•	Capture the Hotel Name, price and reservation details and store in HotelDetails variable
Init state
•	Initialize SystemError variable
•	Invoke GetConfigData workflow
•	Invoke GetValidBandName workflow
•	Validate BandName
•	If Not valid transition again to Input state
•	Catch all possible exceptions, If error transition to error state to print the exceptions.
•	If Valid transition to SearchConcert State
  SearchConcert state
•	Invoke SearchConcert workflow
•	Catch all possible exceptions, if error transition to error state to print the exceptions.
•	If Concert count >0 and Concert in Sydney transition to Display Result to display the result
•	If Concert count >1 and Concert not in Sydney transition to Display Result to display respective result
•	If Concert count = 0 transition to FindAlternativeBandName state
FindAlternativeBandName state
•	Open Search Alternative BandNames URL in chrome browser
•	Maximize window
•	If no results found transition to init state to display relevant message and input new BandName
•	If results found, collect all the Alternative Band Names into a list.
•	For each bandname invoke SearchConcert workflow to check if it returns valid concert.
•	If a valid concert is found assign the alternative bandname to that item and exit the loop
•	According to the transitions conditions display the respect output message using DisplayResult state.
•	Catch all possible exceptions, if error transition to error state to print the exceptions.


