# msfsmissions
 #### Microsoft Flight Simulator Missions

This is a first attempt to create missions in the new Microsoft Flight Simulator.
As for now (and from what I've understood), the only way to add an activity in MSFS is to create
either new LandingChallenges or new BushTrips. This first test mission (in `TestProject`) is declared
as a BushTrip and a thumbnail will be added in the Bushtrip menu of the simulator. Directories are reparsed
every time you open the menu, thus easing testing. You may also modify and compile using the dev tools while
you are flying the mission, you just need to restart the mission for changes to take effect. Before restarting
you need to copy back the compiled package (found in local Packages directory) to the Community directory of your
MSFS installation. I'm not sure but it seems that `.flt` files are not reloaded without restarting the simulator.

#### Mission packages
Compiled mission packages are in the `missionpackages` directory.

*Under TestProject*
- Test mission
  This first mission is derived from the Boeing Field mission found in the Mission Creation Kit of the FSX SDK,
  I just located the mission in La Tuque (Canada PQ).
  There is a [first release archive of the test mission](https://github.com/geehalel/msfsmissions/raw/master/mission-packages/geehalel-testmission-0.1.0.zip), 
  just unzip the file under the Community directory.
- Bushtrip mission `latuque-chalet-cylq`
  This a first attempt to create a bushtrip mission. It does not work as is because each leg should end/start in an airport and I tried to use
  unexisting seaports here (on the numerous lakes found in Canada). However it shows that you may define many packages in a single project.

*Under BushtripStLaurent*
  - Single `stlaurent` bushtrip mission package in its own project. This one uses airports for each leg, and works as axpected. You may use it as a framework
  for your own missions.

#### Creating a package in its own project
- I use in this example the project `BushtripStLaurent`, using `geehalel` as creator. The project will contain only one package, a bushtrip mission package.
- Refering to Asobo practice, I name activity/package in the form `creator-type-name`
- In a MSFS projects directory (whatever you want), create a `BushtripStLaurent` directory.
- In this `BushtripStLaurent` directory:
  - Create a `PackageDefinitions` directory
  - Create a `PackageSources` directory
  - Create a `bushtripstlaurent.xml` file (named after the project name) which enumerates the packages defined in the project:
```
<Project Version="2" Name="BushtripStLaurent" FolderName="Packages">
	<OutputDirectory>.</OutputDirectory>
	<TemporaryOutputDirectory>_PackageInt</TemporaryOutputDirectory>
	<Packages>
		<Package>PackageDefinitions\geehalel-bushtrip-stlaurent.xml</Package>
	</Packages>
</Project>
```
- In the `BushtripStLaurent\PackageDefinitions`, create the `geehalel-bushtrip-stlaurent.xml` referenced above:
```
<AssetPackage Name="geehalel-bushtrip-stlaurent" Version="0.1.0">
	<ItemSettings>
		<ContentType>MISSION</ContentType>
		<Title>Saint-Laurent mission package</Title>
		<Manufacturer/>
		<Creator>geehalel</Creator>
	</ItemSettings>
	<Flags>
		<VisibleInStore>false</VisibleInStore>
		<CanBeReferenced>false</CanBeReferenced>
	</Flags>
	<AssetGroups>
		<AssetGroup Name="stlaurent">
			<Type>Mission</Type>
			<Flags>
				<FSXCompatibility>false</FSXCompatibility>
			</Flags>
			<AssetDir>PackageSources\stlaurent\</AssetDir>
			<OutputDir>Missions\geehalel\BushTrips\stlaurent\</OutputDir>
		</AssetGroup>
	</AssetGroups>
</AssetPackage>
```
I am not sure what the `ContentInfo` asset is used for.
  
- In the `BushtripStLaurent\PackageSources` directory:
  - Create the `PackageSources\stlaurent\` file referenced above
  - Inside this directory, you will have to put your mission files: `stlaurent.flt` which defines your initial flight position, `stlaurent.xml` which defines
  your mission (and will be compiled into a `.spb` file by the `fspackagetool`), `images` subdirectory for activity/briefing/loading/legs/sublegs illustration, 
  `overview.htm` and/or `stlaurent.pln` (draws the flight path in Briefing, NO), sound files...
  - `images/Activity_widget.jpg` (786x602) is used as a thumbnail in the bushtrip menu. Briefing Text is not displayed. I don't know how to create the fly path
  in the 3D wrold map. If `UseLiveWeather` is set to true in the `.flt` file,
  you can not change the current time, but, on the second loading the time is read from the `.flt` file.
  See below how I proceed for other infos.

#### Creating a BushTrip
- Use a flight planner to generate a flight plan in `.pln` format. The flight should have valid departure and destination airports.
You can not use GPS/user defined locations as departure or arrival. I use LittlenavMap in this BushTrip (`latuque-chalets.pln`).
- Start Microsoft Flight Simulator, select the World Map widget, select the aircraft to use in the BushTrip, and then load your
flight plan using `Load/Save` button. You can not load a `.pln` file after you have started the flight. Start the flight.
- Immediately save your flight in the simulator, this will create a `.flt` file we will use in the BushTrip. Note that it creates also
a `.fssave` file and a `.spb` file. They will be saved by default in your user's `AppData\Local\WindowsApps\Microsoft.FlightSimulator...` directory
(`latuque-chalets.flt`).
- Create a new directory for your bushtrip inside your package source directory (`PackageSources\latuque-chalets-cylq`). You may add a `images`
directory inside it (`PackageSources\latuque-chalets-cylq\images`).
- Copy the `.flt` flight file you saved in the simulator in the `PackageSources\latuque-chalets` directory (`PackageSources\latuque-chalets-cylq\latuque-chalets-cylq.flt`).
- Extract from an existing BushTrip `.spb` file the skeleton used for the mission `.xml` file. I won't detail that part here, it should hopefully be soon included in the
SDK documentation. If you know how to extract the `.xml` file, you will find that the XML elements are self-explanatory enough.
- Fill the legs/sublegs of your BushTrip with comments and images as needed. The waypoints in the legs/sublegs are linked to the ones from the `.flt` file
using a fake Region identifier (these are the `!A`, `!B`, ... ids you find in the `.xml` file) and a usual name ID (usually ICAO, here `POI1`, `POI2`, ...) . 
You should modify your `.flt` file accordingly by updating each waypoint in the `ATC_Aircraft.0` waypoints list with its corresponding fake region 
identifier and name ID. For instance:
```
[ATC_Aircraft.0]
ActiveFlightPlan=True
RequestedFlightPlan=False
AcState=ACSTATE_CTAF_TAKEOFF
ActiveVFRAirport=CYLQ
Waypoint.0=, CYLQ, , La Tuque, A, N47° 24.56', W72° 47.34', +000548.00, , , , , , NONE, 0, 0, -1
Waypoint.1=!A, POI1, , Motocross field, U, N47° 23.43', W72° 47.13', +000750.00, , , , , , NONE, 0, 0, -1
Waypoint.2=!A, POI2, , Chemin du Lac Wayagamak, U, N47° 23.43', W72° 45.85', +000900.00, , , , , , NONE, 0, 0, -1
Waypoint.3=!A, POI3, , Intersection, U, N47° 23.82', W72° 44.75', +001050.00, , , , , , NONE, 0, 0, -1
...
Waypoint.9=, , , Chalet Doucet, U, N47° 21.01', W72° 26.69', +002000.00, , , , , , NONE, 0, 0, -1
Waypoint.10=!B, POI1, , Climb south to the powerlines, U, N47° 20.04', W72° 26.28', +002000.00, , , , , , NONE, 0, 0, -1
Waypoint.12=!B, POI2, , Follow the powerlines, U, N47° 14.92', W72° 25.46', +002000.00, , , , , , NONE, 0, 0, -1
```
  In the `.xml` file, you would refer them as follows:
```
...
		<SubLegs>
            <SubLeg>
              <Descr>Take off from La Tuque and search for the Motocross field just right after the end of the runway</Descr>
              <ImagePath>
              </ImagePath>
              <ATCWaypointStart id="CYLQ" />
              <ATCWaypointEnd id="POI1">
                <idRegion>!A</idRegion>
              </ATCWaypointEnd>
            </SubLeg>
			<SubLeg>
              <Descr>When you reach the motocross field, turn left to the east while you climb</Descr>
              <ImagePath>
              </ImagePath>
              <ATCWaypointStart id="POI1">
                <idRegion>!A</idRegion>
              </ATCWaypointStart>
              <ATCWaypointEnd id="POI2">
                <idRegion>!A</idRegion>
              </ATCWaypointEnd>
            </SubLeg>
		</SubLegs>
...
```
- For each leg, you should specify a trigger which signals the end of the leg. This is usually an `AirportLandingTrigger`. In the current BushTrips,
these triggers are extended with specific computations (using speed, runway distance, ...), the XML element being  `AirportLandingTriggerEnd`.
Those computations use a Reverse Polish notation which you see referenced at the end of the SDK documentation. The same computation has to be repeated
in the `.xml` file as it depends on the airport ICAO to get Lat/Lon/Elev. Thus for each leg you should add the same bunch of code to check if the aircraft has correctly
landed on the given airport (I don't know if there will be a way to factor these computations using an airport ICAO parameter). 
- I try to use an `AreaLandingTrigger` instead in this particular BushTrip as we are landing on water here. Looking at the `propmission.xml` file this is not supported.
- The next part concerns the goals and objectives. The `SimMission.MissionBushTrip` element has an `Objectives` element which may reference one or many
`Goal` elements. Those `Goal` elements are triggered as usual using `GoalResolutionAction`. Success is triggered using the `OnFinishedActions` element of 
the `SimMission.MissionBushTrip` element, which should trigger when you end the last leg. Failures may be triggered as usual or using a flight loop computation
(out of fuel in the current BushTrips). What I am not sure is that failures are part of objectives.
- The last part concerns the interaction with the simulator. There seems to be a Finite State Machine (`FlowState` actions/events) used to interact with 
the simulator flight loop/interface. There are predefined events and actions, particularly two predefined objects referenced as actions:
  - `id="RTC_Ground_Airport_Aircraft_Intro" InstanceId="{306B2AE4-06BA-48C3-93A0-BD5569E6EF5B}"`
  - `id="RTC_Ground_Aircraft_Outro" InstanceId="{5DAADB19-3BA3-4235-BCC0-39CD6F4CD4D9}" />`

  They seem to be used as predefined actions between legs. I won't go further here as I would need to know more about the semantics of this FlowState machine.
- As of now I simply reuse the existing elements found in the Asobo scripts. There is no need to generate other GUIDs for those elements in each mission.
   I suppose these objects only exist the time of the mission.
- The Node graph editor in the sim is cool to see what's going on in the mission.

   ![](https://github.com/geehalel/msfsmissions/raw/master/screenshots/nodegraph-stlaurent.png "Node graph editor")
   
#### Some notes for myself
- FSX/ESP 1.0 mission elements are parsed as before. There is a bunch of new elements however.
Better wait all this to be published in the next SDK release.
- Use .GUID app from the Microsoft store to create Guids 
- Images: use jpegs (not sure pngs are ok), Briefing/Loading images may be any size (screenshots),
the Activity_Widget thumbnail should be 786x602. There is a marketplace thumbnail too, which should be 412x170 (not tested).
Use `paint` to edit/resize screenshot captures. 
- Flight starts directly after loading (no preflight screen)
- End of mission: nothing happens in the current version.
- No menu, vfr, atc during the mission, but you can change view
- Try to put multiple missions in a package
- Add scenery objects: use Model GUID, need to decompile BGL files, don't find them in the Scenery Editor
- Add Sim objects: animation may have changed