# ll#include <libplayerc++/playerc++.h>
#include <iostream>
#include <fstream>
#include <cstdlib>
#include <ctime>

    using namespace PlayerCc;

    std::string  gHostname(PlayerCc::PLAYER_HOSTNAME);
    // NOTE !!! PLAYER_PORTNUM is the port number and has to be unique for each student,
   // and be set in the plaer server with -p
    uint32_t        gPort(PlayerCc::PLAYER_PORTNUM); // Replace this with your port number !!
    
    double a = 0.25;
    //srand((unsigned)time(NULL));
    double y = rand() % 6 - 3.14;
    

void random_walk(Position2dProxy* pp);

int main(int argc, char **argv)
{
    srand((unsigned)time(0));

  // we throw exceptions on creation if we fail
  try {

    PlayerClient robot(gHostname, gPort); // Conect to server
    Position2dProxy pp(&robot, 0);   // Get a motor control device (index is 0)

    std::cout << robot << std::endl;

    pp.SetMotorEnable (true); // Turn on Motors


    // go into  a loop
    for(;;){
      double newspeed = .5 ;
      double newturnrate = 0;

      // this blocks until new data comes; 10Hz by default
      robot.Read();

      // write commands to robot
      random_walk(&pp);
    }
  }
  catch (PlayerCc::PlayerError & e) {
    std::cerr << e << std::endl; 
    return -1;
  }
}

void random_walk(Position2dProxy* pp) {
    double x = rand() % 6 - 3.14;
    y = (1-a) * y + a*x;
    std::ofstream coordinatesOutput;
    coordinatesOutput.open("output.txt", std::ios_base::app);
    coordinatesOutput << pp->GetXPos() << "," << pp->GetYPos() << "\n";
    coordinatesOutput.close();
    pp->SetSpeed(1, y);
}
