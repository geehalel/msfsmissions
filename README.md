# msfsmissions
 #### Microsoft Flight Simulator Missions

This is a first attempt to create missions in the new Microsoft Flight Simulator.
As for now (and from what I've understood), the only way to add an activity in MSFS is to create
either new LandingChallenges or new BushTrips. This first test mission (in `TestProject`) is declared
as a BushTrip and a thumbnail will be added in the Bushtrip menu of the simulator. Directories are reparsed
every time you open the menu, thus easing testing. You may also modify and compile using the dev tools while
you are flying the mission, you just need to restart the mission for changes to take effect. Before restarting
you need to copy back the compiled package (found in local Packages directory) to the Community directory of your
MSFS installation.

#### Mission packages
Compiled mission packages are in the `missionpackages` directory.

This first mission is derived from the Boeing Field mission found in the Mission Creation Kit of the FSX SDK,
I just located the mission in La Tuque (Canada PQ).

There is a [first release archive of the test mission](missionpackages/geehalel-testmission-0.1.0.zip), just unzip the file under the Community directory.

#### Some notes for myself
- FSX/ESP 1.0 mission elements are parsed as before. There is a bunch of new elements however.
Better wait all this to be published in the next SDK release.
- Images: use jpegs (not sure pngs are ok), Briefing/Loading images may be any size (screenshots),
the Activity_Widget thumbnail should be 786x602. There is a marketplace thumbnail too, which should be 412x170 (not tested).
Use `paint` to edit/resize screenshot captures. 
- Flight starts directly after loading (no preflight screen)
- End of mission: nothing happens in the current version.
- No menu, vfr, atc during the mission, but you can change view
- Try to put multiple missions in a package