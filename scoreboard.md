---
layout: page
title: Live Scoreboard
subtitle: With RGB LED Matrix and Raspberry Pi
share-img: /assets/img/scoreboard/favicon.png
---

<section id="objective">
    <div class="section-title">
        <h2>Objective</h2>
    </div>
    <div class="container">
        <p> Bring the excitement of going to a sporting event in-person to the comfort of your own home! Using a
            Raspberry Pi connected to a 64x32 LED matrix, this miniature scoreboard pulls and displays live
            sports scores
            from the ESPN API. The scoreboard allows the user to select from many sports and teams, both
            collegiate and
            professional, using the built-in PiTFT touch screen. The scoreboard displays pre-game information,
            in-game
            scores, and post-game results.</p>
    </div>
</section>

<section id="intro">
    <div class="section-title">
        <h2>Introduction</h2>
    </div>
    <div class="container">
        <p>
            This project emulates a scoreboard by using a Raspberry Pi connected to a 64x32 LED matrix. Using
            the touch
            screen on the PiTFT, the user is able to select a team to display from collegiate and professional
            hockey,
            football, and basketball. The user can also
            select to rotate between teams instead of displaying only one team. The system then queries the ESPN
            API to
            get live scores from the internet according
            to the user's settings. Finally, the team names, logos, and scores are displayed on the LED matrix.
            The
            scoreboard displays a celebration whenever a team scores in Football or Hockey.
            <br> <br>
            When the selected team is not currently in a live game, the scoreboard displays the results of the
            previous
            game or the date, time, and opponent of the next game, depending on which is closer in time. Using
            cron, the service starts on boot and defaults to displaying Cornell Men's Hockey.

            <!-- For a given sport
          and league, the user also has the option to select <code>All</code> instead of a specific team. This feature
          then rotates -->
        </p>

    </div>
</section>

<section id="video">
      <div class="section-title">
        <h2>Demo Video</h2>
        <div class="embed-responsive embed-responsive-16by9">
          <iframe width="560" height="315" src="https://www.youtube.com/embed/BymvAE_KxuY?si=KLNyOlMyyj0KMYOD"
            title="Demo Video" allowfullscreen></iframe>
        </div>
      </div>
    </section>

<section id="design">
    <div class="section-title">
        <h2>Design & Testing</h2>
    </div>
    <div class="container">
        <!-- Design and Testing: Include design steps involved in the project. Please include any issues you
          experienced as well as well as smooth progress in the various phases of your project. Describe testing you
          performed to confirm that development steps of the project performed as planned. -->


        <h4><br>Hardware Design and Testing</h4>
        <p>As seen in <b>Figure 1</b>, the hardware consists of three major components: a Raspberry Pi 4, a
            touchscreen
            PiTFT, and a 64x32 LED Matrix. The Raspberry Pi contains a 16 GB SD card with the Lab 3 kernel
            installed, and
            is connected to power and the PiTFT in the same way as in Lab 3. The LED Matrix requires an
            additional 13
            GPIOs to connect, fortunately none of which conflict with the PiTFT. To power the LED Matrix, the
            included
            Power and Ground connector cable gets plugged into the back of the LED matrix on one end and screwed
            into a
            Female DC Power adapter on the other. Finally, the DC power adapter is plugged into a 5V 2A power
            cable.</p>

        <div class="center">
            <br>
            <p><strong>Figure 1: System Overview</strong></p>
            <img src="/assets/img/scoreboard/f1.png" alt="" style="width: 90%">
            <br>
            <br>
            <br>
        </div>

        <p><b>Table 1</b> shows the correct pinout for connecting the LED Matrix to the Raspberry Pi [1]. LED
            Matrix
            pins are
            numbered left to right, top to bottom.</p>

        <div class="center">
            <br>
            <p><strong>Table 1: Raspberry Pi to LED Matrix Wiring</strong></p>
            <table class="table">
                <tr>
                    <th>Pi Pin</th>
                    <th>Matrix Pin</th>
                    <th>Signal Name</th>
                </tr>
                <tr>
                    <td>GPIO 5</td>
                    <td>1</td>
                    <td>Red Row 1</td>
                </tr>
                <tr>
                    <td>GPIO 13</td>
                    <td>2</td>
                    <td>Green Row 1</td>
                </tr>
                <tr>
                    <td>GPIO 6</td>
                    <td>3</td>
                    <td>Blue Row 1</td>
                </tr>
                <tr>
                    <td>GPIO 12</td>
                    <td>5</td>
                    <td>Red Row 2</td>
                </tr>
                <tr>
                    <td>GPIO 16</td>
                    <td>6</td>
                    <td>Green Row 2</td>
                </tr>
                <tr>
                    <td>GPIO 23</td>
                    <td>7</td>
                    <td>Blue Row 2</td>
                </tr>
                <tr>
                    <td>GPIO 22</td>
                    <td>9</td>
                    <td>Line Selection A</td>
                </tr>
                <tr>
                    <td>GPIO 26</td>
                    <td>10</td>
                    <td>Line Selection B</td>
                </tr>
                <tr>
                    <td>GPIO 27</td>
                    <td>11</td>
                    <td>Line Selection C</td>
                </tr>
                <tr>
                    <td>GPIO 20</td>
                    <td>12</td>
                    <td>Line Selection D</td>
                </tr>
                <tr>
                    <td>GPIO 17</td>
                    <td>13</td>
                    <td>CLK</td>
                </tr>
                <tr>
                    <td>GPIO 21</td>
                    <td>14</td>
                    <td>Latch</td>
                </tr>
                <tr>
                    <td>GPIO 4</td>
                    <td>15</td>
                    <td>Output Enable</td>
                </tr>
                <tr>
                    <td>GND</td>
                    <td>4, 8, 16</td>
                    <td>GND</td>
                </tr>
            </table>
            <br>
        </div>

        <p>Testing the hardware involved wiring everything up and seeing if the matrix would display simple
            text.
            Initially, one source gave incorrect information, saying Pin 12 on the LED matrix should be
            connected to GND.
            This wiring is only true for 32x16 LED matrices. For the larger 64x32 matrix, connecting this pin to
            GND
            resulted in the bottom half of the matrix being dark and the top half working perfectly. After
            changing Pin 12
            to GPIO 20, this issue was resolved. <b>Figure 2</b> shows an image of the system after it has been
            wired
            correctly.</p>
        <div class="center">
            <br>
            <p><strong>Figure 2: Final System and Wiring</strong></p>
            <img src="/assets/img/scoreboard/f2.png" alt="" style="width: 70%">
            <br>
            <br>
            <br>
        </div>
        <br> <br> <br>
        <h4>Software Design and Testing</h4>
        <h5>Installation</h5>
        <p>
            The LED Matrix requires one additional Python module called <code>rpi-rgb-led-matrix</code> which
            contains
            functions to draw text and images on the LED Matrix. To install the tool, download the shell script
            from
            Adafruit's official GitHub page <a
                href="https://github.com/adafruit/Raspberry-Pi-Installer-Scripts/blob/main/rgb-matrix.sh">here</a>
            [2]. Once
            the script is downloaded, run the script and select "Adafruit RGB Matrix Bonnet" when prompted to
            choose
            between two different Raspberry Pi Wiring configurations. After rebooting, the packages are
            installed and
            <code>import rgbmatrix</code> in Python will succeed.
        </p><br>
        <h5>Matrix Object</h5>
        <p>The Matrix Object, found in <code>matrix.py</code>, is a general class for configuring the LED
            matrix. The
            class initialization configures the many <code>rgbmatrix</code> options for the specific LED Matrix
            that
            differ from the library's default values. Rows were set to 32, columns set to 64, and brightness set
            to 80.
            Finally, <code>rpi-gpio-slowdown</code>, which is used to ensure the fast RPi 4 does not write to
            the LED
            Matrix too quickly, is set to the maximum slowdown, 4. The initialization of the class also defines
            small,
            medium, and large fonts as well as black and white colors. After these values are set, the LED
            Matrix is ready
            to be used. This class also contains some helper functions to be used by the scoreboard to reduce
            code
            duplication. Resize logo takes an image and
            resizes it to a new height. Draw box uses the led matrix <code>draw_line</code> function to draw a
            box with
            given dimensions and color. Hex to RGB converts hex colors to RGB colors. Invert logo takes an
            all-black
            image, which cannot be displayed on the Matrix, and converts it to an all-white image. Finally,
            ordinal takes
            a number, like <i>1</i>, and converts it to <i>1st</i>.</p>
        <p>
            Testing the matrix object involved displaying random text and images in various sizes and colors, as
            seen in
            <b>Figure 3</b>. At first, the text was
            illegible, which was because <code>rpi-gpio-slowdown</code> was not set to slow enough. After
            correcting this
            mistake, the text could be seen. The text was then drawn in different sizes and colors to test the
            various
            other functions and fonts. Finally, the Cornell Athletics logo was resized and displayed to ensure
            the image
            functions were working properly. One issue with resizing images was that there was heavy aliasing
            when
            downsizing an image to just 20x20. Using the Python Image Library (PIL), the resize image function
            was edited
            to include Bicubic Interpolation, which created smoother and more visually pleasing imaged on the
            tiny LED
            Matrix screen. After the Matrix object was complete, it was time to move onto making the actual
            Scoreboard.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 3: Testing the Matrix by Displaying Text and Images</strong></p>
            <img src="/assets/img/scoreboard/f3.png" alt="" style="width: 60%">
            <br>
            <br>
            <br>
        </div><br>

        <h5>The Scoreboard</h5><br>
        <h6><u>Parsing Data</u></h6>
        <p>The Scoreboard calls the matrix object as a subclass, so it gets initialized with all of the options,
            fonts,
            colors, and helper functions configured previously. Upon launch, the scoreboard reads the default
            values for
            sport, league, and team from a configuration file called <code>config.yml</code>. For this project,
            these
            values were configured respectively to be Hockey, Men's NCAA Hockey, and Cornell Hockey. The
            scoreboard
            proceeds to send a
            HTTP GET request to ESPN's API using the provided values of sport and league. ESPN's API is
            unofficial, but
            other people have discovered that the format for such an API request is
            <code>http://site.api.espn.com/apis/site/v2/sports/{SPORT}/{LEAGUE}/scoreboard</code> [3]. This
            project
            implements the three sports Hockey, Basketball, and Football for the sport's American professional
            league as
            well as the men's and women's (where applicable) NCAA league.
        </p>
        <p>
            The HTTP GET request to the API returns a JSON that must be parsed in order to display the scores on
            the
            scoreboard. This JSON parsing required lots of testing and trial and error, since the values
            returned by the
            API were sport-dependent and status dependent. For example, <code>timeouts-remaining</code> is
            applicable for
            Basketball and Football but not for Hockey. However, if the game is not in progress but instead is
            scheduled
            or has completed, there is no key in the JSON for timeouts remaining. The strategy used to solve
            this issue
            was to inject default values for each key that is missing. Then, the JSON can be parsed without
            throwing a
            <code>KeyNotFoundError</code> and crashing the entire script.
        </p>
        <p>The JSON contains a list of each team in the league's next or most previous competition. The list is
            looped
            through until the team matches the team given in <code>config.yml</code>. If for some reason that
            team is not
            present in the list, the first team's competition is instead returned.</p>
        <p>
            Now that the competition has been extracted from the JSON, it can get parsed into custom structs,
            which are
            found in <code>consts.py</code>. These structs, <code>TeamData</code> and Situation, contain all of
            the data
            necessary to fully describe the competition. The competition's two teams get parsed into
            <code>TeamData</code>, which contains the team's colors, rank, record, logo, 3-4 character
            abbreviation, and
            score. <code>Situation</code> contains the data about the competition, such as time, period,
            possession,
            timeouts remaining, last play, and ball location. While not all of these values apply to every
            sport, setting
            a value to None will cause it to not be inappropriately displayed. Now that all data has been
            extracted from
            the JSON, it is time to finally display the data on the LED Matrix.
        </p><br>
        <h6><u>Displaying Data</u></h6>
        <p>Data is displayed based on the status of the game which is one of pre-game, in-game, or post-game. If
            the
            game status is <code>STATUS_SCHEDULED</code> then the game has
            not started yet and there are no scores to display. The team logos, abbreviations, ranks, and
            records are
            displayed
            along with the date and time of the event. Not all of this data can fit on the screen at once, as
            shown
            in <b>Figure 4</b>. Instead, data is cycled through in 8-second periods. The date of the matchup and
            the team
            abbreviations are shown for 4 seconds, then the time
            of the matchup and the team records are shown for 4 seconds. The sport of the game is represented
            by either a small football,
            basketball, or hockey sticks in the center of the screen.</p>
        <div class="center">
            <br>
            <p><strong>Figure 4: Pre-Game Display of a Cornell Hockey Matchup</strong></p>
            <img src="/assets/img/scoreboard/f4.png" alt="" style="width: 60%">
            <br>
            <br>
            <br>
        </div><br>

        <p>
            During the game, when status is <code>STATUS_IN_PROGRESS</code> is when the most data is needed to
            fully
            describe the situation. Therefore, when in-game, the
            logos are shrunk to 1/4th the size as they were in pre-game and placed on the right side of the
            screen. Boxes
            encapsulating the logos, team abbreviations, and scores are drawn according to the team's color.
            Timeouts
            remaining and possession,
            if applicable, are indicated with dashes and dots respectively, just as they are on a real
            scoreboard. The
            left side of the
            screen lists the period and time remaining in the period, as well as the down & yards to go, and
            location of
            the ball if applicable. <b>Figure 5</b> shows an in-game Cornell Hockey game and <b>Figure 6</b>
            shows an
            in-game NFL
            game
            which therefore also includes down & yards to go, location of the football, timeouts remaining for
            each team,
            and possession.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 5: In-Game Display of a Cornell Hockey Matchup</strong></p>
            <img src="/assets/img/scoreboard/f5.png" alt="" style="width: 60%">
            <br>
            <br>
            <br>
        </div>

        <div class="center">
            <br>
            <p><strong>Figure 6: In-Game Display of a Professional Football Matchup</strong></p>
            <img src="/assets/img/scoreboard/f6.png" alt="" style="width: 60%">
            <br>
            <br>
            <br>
        </div><br>

        <p>
            When the <code>previous_play</code> field during an in-game Hockey or Football matchup indicates
            that points
            have been scored, a celebration animation is displayed. The animation displays blinks the name of
            the play
            (goal,
            touchdown, etc.), the name of the team, and a full-screen team logo in 1-second intervals. After 5
            blinks, the
            animation is over and the score of the game returns. The animation can be seen in <b>Figure 7</b>.
        </p>

        <div class="center">
            <br>
            <p><strong>Figure 7: Scoring Animation for Cornell Hockey</strong></p>
            <img src="/assets/img/scoreboard/f7.gif" alt="" style="width: 60%">
            <br>
            <br>
            <br>
        </div><br>
        <p>At the end of the game, when status is <code>STATUS_FINAL</code>, the final score must be displayed.
            Shown in
            <b>Figure 8</b>, the status of the game is seen at the top of the LED Matrix, with the logos in a
            similar
            position to
            the pre-game view. The scores and the sport logo are shown in the center of the screen.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 8: Final Score for Cornell Hockey</strong></p>
            <img src="/assets/img/scoreboard/f8.png" alt="" style="width: 60%">
            <br>
            <br>
            <br>
        </div><br>
        <p>To test these various states and display configurations, JSONs were downloaded from the API and
            loaded into
            the Scoreboard instead of being pulled from the API in real-time. This hack kept all parameters
            static so
            modes could be tested and verified without changing values. One issue encountered is some team's
            colors were
            so bright, or white, that when using this color as a background for in-game display, the score could
            not be
            seen on top of it. The solution was to make a function that decreased the brightness of the
            background
            rectangle so the text on top could be seen better. Another quick and dirty solution is a
            <code>teams_to_invert</code> list in Scoreboard class. Members of this list, such as the Iowa
            Hawkeyes, have
            either black logos or white team color. After inversion, the team logo is changed to white and the
            team color
            is changed to black to promote better visibility of the team's logo and score.
        </p>
        <p>
            Another problem faced occurred when running the system for an extended period of time on RedRover,
            the
            firewall started to block the POST requests to ESPN. The solution was to connect the Raspberry Pi to
            eduroam based on instructions from UCSC engineering [4].
        </p>

        <br>
        <h5>Pygame on the PiTFT</h5>
        <p>While the scoreboard displays competition data, the PiTFT runs a touch-screen GUI using Pygame to
            allow the
            user to select
            the sport, league, and team to display. Shown in <b>Figure 9</b>, the GUI offers three sets of arrow
            keys, one
            for each sport, league, and team. The options for league depend on the selection for sport, and the
            options
            for team depend on the selection for league. When the desired parameters are reached, the
            <code>SEND PARAMETERS</code> button is pressed to send the new values to the scoreboard. This button
            updates
            the global variables that are read by the scoreboard when pulling from the API and selecting a
            competition
            from the returned JSON.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 9: Touch-Screen PyGame GUI for selecting Sport, League, and Team</strong></p>
            <img src="/assets/img/scoreboard/f9.png" alt="" style="width: 45%">
            <br>
            <br>
            <br>
        </div><br>
        <p>
            The final button on the GUI, <code>START DEMO</code>, starts the demo. When the button is pushed, a
            the global
            variable
            <code>in_demo</code> is set to True. In this state, instead of querying the ESPN API for new JSONs,
            the JSONs
            are pulled from the local filesystem. Pulling from the local filesystem to maintain consistency was
            used for
            previous testing, so the only additional code that had to be done was pull the correct JSON for the
            given
            state of the demo. The demo intends to highlight the four different phases of a Cornell Hockey game:
            pre-game,
            in-game, goal scored, and final. Shown in <b>Figure 10</b>, the user can click back and forth
            between states
            and go back to live scores by ending the demo when finished.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 10: The System in Demo Mode</strong></p>
            <img src="/assets/img/scoreboard/f10.png" alt="" style="width: 45%">
            <br>
            <br>
            <br>
        </div><br>
        <p>
            Given a sport and league, the GUI also allows the user to select "All" or a specific league instead
            of one
            team. If one of these special options are selected, the scoreboard will display each competition in
            the pulled
            JSON for 8 seconds, looping back to the beginning after all matchups have been displayed. <b>Figure
                11</b>
            shows the system when Ivy League men's basketball has been selected. Each upcoming men's basketball
            game
            involving an Ivy League is shown for 8 seconds in a loop.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 11: Rotating Through All Teams in Ivy League Basketball</strong></p>
            <img src="/assets/img/scoreboard/f11.png" alt="" style="width: 90%">
            <br>
            <br>
            <br>
        </div><br>
        <p>
            <br>
        <h5><code>start.sh</code> and <code>cron</code></h5>
        <p>
            Since this is an embedded system, the scoreboard must start up on boot and not require a keyboard
            and mouse to
            activate. Using cron, <code>start.sh</code> is called on reboot and starts up the system. The
            two-line
            <code>start.sh</code> accepts inputs for delay and username, allowing optional arguments for
            brightness, size
            of the matrix, and <code>rpi-gpio-slowdown</code>. Line 1 delays for the given number of seconds to
            allow the
            system time to connect to internet before attempting to pull from the ESPN API. Line 2 is a sudo
            call to
            launch <code>main.py</code> with the given input for username. The intention is to use absolute
            filepaths for
            both <code>main.py</code> and the fonts used on the matrix, so the user must specify their username
            if theirs
            is different
            than the default value of <code>pi</code>.
        </p>
        <p>Testing is straightforward, just as learned in lecture. By running and confirming
            <code>start.sh</code> works
            from the /tmp
            folder, it can be ensured that <code>start.sh</code> will work from wherever cron runs it from.
        </p>


    </div>
</section>

<section id="results">
    <div class="section-title">
        <h2>Results</h2>
    </div>
    <div class="container">
        <p>As a whole, everything performed as expected and the goals outlined in the initial project
            description were
            met.
            The scoreboard pulls live scores from the ESPN API and displays them on the LED Matrix as planned.
            The
            Raspberry Pi, PiTFT, and LED Matrix work together in harmony to provide an embedded system complete
            with a GUI
            and plenty of compute and memory to spare. </p>
        <p>The scoreboard can transition between the three states of pre-game, in-game, and post-game without
            the need
            for human interaction. The demo mode works very well for showcasing the basic features of the
            system, but
            definitely could have been improved to showcase other sports or leagues. The extra features
            implemented, such
            as displaying other sports besides hockey, rotating through multiple
            teams, and flashing the team logo when a goal is scored, all served to make the end result even
            better. The
            ESPN API along with the 4 second refresh rate provided the perfect amount of delay when watching
            games. The
            scoreboard and television were in sync enough that the scoreboard was not spoiling the action by
            being ahead
            or unrelated to the action by being too far behind.</p>
        <p>There were many bugs experienced when relying on an external API to generate the JSONs needed for
            displaying
            data. In leagues such as NCAA Women's Hockey, which is not nearly as popular as the NFL, ESPN is
            often missing
            games or details. For these less popular leagues, the pre-game and post-game results may be shown,
            but ESPN
            does not show live scores for in-game. Errors were handled very well, allowing top-notch performance
            and
            reliability. Default values were assigned when parsing JSONs so the script would never exit.
            Furthermore, if
            the user selects a team that is not found in the JSON, the scoreboard will simply display the first
            competition in the list for the selected sport and league. </p>
        <p>Taking photos of the system was surprisingly difficult. Although impossible to see with the naked
            eye, the
            LED Matrix does not light up all LEDs at the same time. As seen in <b>Figure 12</b>, only half of
            the
            screen is lit up at any instant. Most pictures taken of the matrix look similar to this, and the
            only way to
            take good pictures of the Matrix was to decrease shutter speed for a longer exposure.</p>
        <div class="center">
            <br>
            <p><strong>Figure 12: Most Photos of the System Have Missing Pixels</strong></p>
            <img src="/assets/img/scoreboard/f12.png" alt="" style="width: 80%">
            <br>
            <br>
            <br>
        </div><br>
        <p>
            Hardware is not my expertise, and mounting/casing was admittedly an afterthought. Fortunately, the
            LED Matrix
            came with magnets which were screwed into the back. After taping the PiTFT and Pi in place under the
            LED
            Matrix, the entire scoreboard system could be magnetically suspended on any surface. The system is
            now an
            all-in-one, portable scoreboard ready to be mounted anywhere, as shown in <b>Figure 13</b>.
        </p>
        <div class="center">
            <br>
            <p><strong>Figure 13: System Mounting</strong></p>
            <img src="/assets/img/scoreboard/f13.png" alt="" style="width: 80%">
            <br>
            <br>
            <br>
        </div><br>
    </div>
</section>

<section id="conclusion">
    <div class="section-title">
        <h2>Conclusions</h2>
    </div>
    <div class="container">
        <p>The goal of bringing the excitement of an in-person sporting event to the comfort of the home by
            means of a
            miniature scoreboard was met. The scoreboard pulls live scores from ESPN API and displays them in
            real-time to be viewed at a glance. The user can select from over 200 different teams across three
            different
            sports with the use of an intuitive touch-screen GUI. The user can also choose to cycle through all
            teams of a
            given sport and league or
            teams in a specific conference. The user can then watch one game on television while keeping
            up-to-date with
            other games occurring simultaneously. </p>
        <p>At first, a 32x16 LED Matrix was attempted to be used for this project, which has just 1/4 the number
            of
            pixels as the 64x32 matrix. The smallest dimensions to make a 26-character alphabet is 4x6 so it
            only possible
            to display 8 characters on one line and less than 3 lines of text total [5]. Therefore, it was
            absolutely
            necessary to use the larger 64x32 matrix. A higher resolution matrix was considered but did not line
            up with
            the goals of this project, which was to bring a retro scoreboard feel to the home environment.</p>

        <p>The scoreboard and GUI leverage cron to begin on reboot and function as expected without
            interruption.</p>

    </div>
</section>

<section id="future">
    <div class="section-title">
        <h2>Future Work</h2>
    </div>
    <div class="container">
        <p>This project uses very little computation and memory from the Raspberry Pi 4. One hardware
            improvement to be
            made to the system that cuts down on cost is to use a Raspberry Pi Zero W. Continued work could
            include swapping out the $35
            Raspberry Pi 4 for a $15 Raspberry Pi Zero W and verifying the system still works as expected.</p>

        <p>One software feature that is nice to include in the future is to allow the user to rotate between
            a custom list of teams from various leagues. While the system currently allows the user to rotate
            between
            different games in a given league, such as rotating through Ivy League Basketball, the system does
            not allow
            for customization. The proposed feature allows the user to add teams to a list to be rotated
            through,
            such as all sports teams from a given city or university.</p>

        <p>Finally, one thing that I wish I had time for is to add a speaker to the system. To more closely
            mimic
            in-person sporting events, a siren could go off whenever a goal is scored in hockey, and cheering or
            the
            team's anthem could be played whenever a touchdown is scored in football. This feature would require
            both a
            hardware and a software addition.</p>


    </div>
</section>

<section id="budget">
    <div class="section-title">
        <h2>Budget and Bill of Materials</h2>
    </div>
    <div class="container">
        <div class="center">
            <table class="table">
                <tr>
                    <th>Item</th>
                    <th>Quantity</th>
                    <th>Total Cost ($)</th>
                </tr>
                <tr>
                    <td>Raspberry Pi 4</td>
                    <td>1</td>
                    <td>35</td>
                </tr>
                <tr>
                    <td>16 GB microSD Card</td>
                    <td>1</td>
                    <td>8</td>
                </tr>
                <tr>
                    <td>15W USB-C Power Supply</td>
                    <td>1</td>
                    <td>8</td>
                </tr>
                <tr>
                    <td>Resistive Touch PiTFT - 320x240 2.8" TFT+Touchscreen</td>
                    <td>1</td>
                    <td>35</td>
                </tr>
                <tr>
                    <td>64x32 P4 RGB LED Matrix Panel</td>
                    <td>1</td>
                    <td>36</td>
                </tr>
                <tr>
                    <td>Female DC Power adapter</td>
                    <td>1</td>
                    <td>2</td>
                </tr>
                <tr>
                    <td>5V 2A DC Power Adapter</td>
                    <td>1</td>
                    <td>9</td>
                </tr>
                <tr>
                    <td>Pin to Socket Jumper Cable</td>
                    <td>16</td>
                    <td>2</td>
                </tr>
                <tr>
                    <td><b>TOTAL</b></td>
                    <td>--</td>
                    <td><b>135</b></td>
                </tr>
                <tr>
                    <td>ECE 5725 Basic Kit (Pi, PiTFT, SD Card, USB-C)</td>
                    <td>1</td>
                    <td>(86)</td>
                </tr>
                <tr>
                    <td><b>TOTAL EXCLUDING ECE 5725 BASIC KIT</b></td>
                    <td>--</td>
                    <td><b>49</b></td>
                </tr>
            </table>
            <br>
        </div>

    </div>
</section>

<section id="references">
    <div class="section-title">
        <h2>References</h2>
    </div>
    <div class="container">
        <p>[1] Adafruit Industries, "Adafruit RGB Matrix Bonnet for Raspberry Pi," 2023.
            <a
                href="https://learn.adafruit.com/adafruit-rgb-matrix-bonnet-for-raspberry-pi/?view=all">https://learn.adafruit.com/adafruit-rgb-matrix-bonnet-for-raspberry-pi/?view=all</a>
            (accessed November 6, 2023).
        </p>
        <p>[2] Adafruit Industries, "Raspberry-Pi-Installer-Scripts," 2023. <a
                href="https://github.com/adafruit/Raspberry-Pi-Installer-Scripts/blob/main/rgb-matrix.sh">https://github.com/adafruit/Raspberry-Pi-Installer-Scripts/blob/main/rgb-matrix.sh</a>
            (accessed November 6,
            2023).</p>
        <p>[3] A. Easwaran, "ESPN hidden API Docs," 2019. <a
                href="https://gist.github.com/akeaswaran/b48b02f1c94f873c6655e7129910fc3b">https://gist.github.com/akeaswaran/b48b02f1c94f873c6655e7129910fc3b</a>
            (accessed November 29, 2023).</p>
        <p>[4] Internetworking Research Group — UCSC, "HOWTO Connect Raspberry Pi to Eduroam Wi-Fi," 2019. <a
                href="https://inrg.engineering.ucsc.edu/howto-connect-raspberry-to-eduroam/">https://inrg.engineering.ucsc.edu/howto-connect-raspberry-to-eduroam/</a>
            (accessed November 10, 2023).</p>
        <p>[5] robey, "A very tiny, monospace, bitmap font," 2010. <a
                href="https://robey.lag.net/2010/01/23/tiny-monospace-font.html">https://robey.lag.net/2010/01/23/tiny-monospace-font.html</a>
            (accessed December 6, 2023).
        </p>
        <p>[6] Pygame, "pygame v2.6.0 documentation - pygame.display," 2023. <a
                href="https://www.pygame.org/docs/ref/display.html">https://www.pygame.org/docs/ref/display.html</a>
            (accessed November 20, 2023)</p>
        <p>[7] J. Skovira, “ECE 5725 Lab 3,” 2023. Lab 3_Fall2023_v1.pdf (accessed November 17, 2023).</p>
    </div>
</section>