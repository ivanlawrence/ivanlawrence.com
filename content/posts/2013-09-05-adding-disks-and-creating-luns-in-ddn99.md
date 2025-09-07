+++
date = "Thu, 05 Sep 2013 23:00:00 +0000"
title = "Adding Disks and Creating LUNs in DDN9900 and DDN S7000 Chassis"
slug = "[SLUG]"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# Adding Disks and Creating LUNs in DDN9900 and DDN S7000 Chassis

Installing disks into a DDN enclosure
=====================================

There are four different enclosure configurations for a DDN based SAN: 5, 10, or 20 enclosures

### 5 Enclosures (SS7000\_1X Enclosure mapping)

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

A

B

FRONT

### 10 Enclosures (SS7000\_2X Enclosure mapping)

BACK

I/O Modules  
PSU

49

50

51

52

53

54

DIMMs

55

56

57

58

59

60

37

38

39

40

41

42

43

44

45

46

47

48

25

26

27

28

29

30

31

32

33

34

35

36

13

14

15

16

17

18

19

20

21

22

23

24

1

2

3

4

5

6

7

8

9

10

11

12

A

FRONT

### 20 Enclosures (SS7000\_2X Enclosure mapping)

BACK

I/O Modules  
PSU

49

50

51

52

53

54

DIMMs

55

56

57

58

59

60

37

38

39

40

41

42

43

44

45

46

47

48

25

26

27

28

29

30

31

32

33

34

35

36

13

14

15

16

17

18

19

20

21

22

23

24

1

2

3

4

5

6

7

8

9

10

11

12

A

FRONT

BACK

I/O Modules  
PSU

109

110

111

112

113

114

DIMMs

115

116

117

118

119

120

97

98

99

100

101

102

103

104

105

106

107

108

85

86

87

88

89

90

91

92

93

94

95

96

73

74

75

76

77

78

79

80

81

82

83

84

61

62

63

64

65

66

67

68

69

70

71

72

A

FRONT

Here is an example of a 5 enclosure install already populated with "14 tiers" of disks:

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

A

B

FRONT

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

C

D

FRONT

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

E

F

FRONT

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

G

H

FRONT

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

P

S

FRONT

Tier Information:
-----------------

                         Space
             Capacity  Available     Disk
Tier Owner     (MiB)     (MiB)      Status                LUN List
-----------------------------------------------------------------------------
   1 +  1     4578344          0  ABCDEFGHPS  0 
   2 +  2     4578344          0  ABCDEFGHPS  1 
   3 +  1     4578344          0  ABCDEFGHPS  2 
   4 +  2     4578344          0  ABCDEFGHPS  3 
   5 +  1     4578344          0  ABCDEFGHPS  4 
   6 +  2     4578344          0  ABCDEFGHPS  5 
   7 +  1     4578344          0  ABCDEFGHPS  6 
   8 +  2     4578344          0  ABCDEFGHPS  7 
   9 +  1     4578344          0  ABCDEFGHPS  8 
  10 +  2     4578344          0  ABCDEFGHPS  9 
  11 +  1     4578344          0  ABCDEFGHPS  10 
  12 +  2     4578344          0  ABCDEFGHPS  11 
  13 +  1     4578344          0  ABCDEFGHPS  12 
  14 +  2     4578344          0  ABCDEFGHPS  13 

Verifying TIER 1, 38.3%, Continuous.
Verifying TIER 3,  0.4%, Continuous.
Verifying TIER 8, 83.1%, Continuous.
Verifying TIER 10, 71.2%, Continuous.
All disks are healthy.

  Automatic disk restarting:   Disabled
  Automatic disk rebuilding:   Enabled
  Automatic SMART replacement: Disabled
  Maximum active rebuilds:     4
  Maximum active verifies:     2
  System rebuild extent:       64 MiB
  System rebuild delay:        10
  System delay IO limit:       0
  Journaling:                  Enabled                                     
                                     
              System Capacity 64096816 MiB, 0 MiB available.

If you were to add additional drives, say 30 drives for a total of 3 new tiers (10 drives per tier), you would add them to "15", "16", and "17" of each of the five enclosures.

BACK

I/O Modules  
PSU

25

26

27

28

29

30

DIMMs

25

26

27

28

29

30

19

20

21

22

23

24

19

20

21

22

23

24

13

14

15

16

17

18

13

14

15

16

17

18

7

8

9

10

11

12

7

8

9

10

11

12

1

2

3

4

5

6

1

2

3

4

5

6

A

B

FRONT

...

 Once the disks are installed you will create internal LUNs.  For ease of configuration use one LUN per Tier.  You will use the command `lun add=<LUN#>`:

NOTE: LUN numbers start at "0" but Tier numbers start at "1".  In our example, Tier 15 will be used to create LUN 14 which we label "LUN14"

S2A 9900\[1\]: **lun add=14**

Enter a label for LUN 14 (up to 16 characters): **LUN14**

Enter the capacity (in Mbytes) for LUN 14, LUN14

0 for all available capacity (default): **0**

Enter the number of tiers (1..8)

Default will auto select, 'e' to escape: **1**

Enter the tiers, each one on a new line, or 'e' to escape:
**15**

Enter the block size, (512, 1024, 2048, 4096)
Default is 512, 'e' to escape: **512**

Attempting to add LUN 14
Operation successful: LUN 14 was added to the system.

The LUN must be formatted before it can be used.

Would you like to format the LUN now? (y/N): **Y**

INFO INIT\_137 1-22 18:48:21 Starting Format of LUN 14

NOTE: The lines that say `(default)` in them will allow simply hitting the `ENTER` key to apply the default value instead of entering a value.

Repeat the above for each internal LUN / Tier that has been added.

NOTE: Each LUN / Tier will be "owned" by the controller that was used to create it.  To "load balance" the creation of LUNs you can alternate creation between both controllers.

###  The following will need to be done on each controller individually

Next we need to present, or map, the internal LUN / Tier through the fiber ports as "external" LUNs.  This is done using the `zoning edit=n` command:

NOTE: `zoning edit` can be invoked without specifying a port number (`=n`) in which case you will be asked to specify a port number.

  S2A 9900\[1\]: **zoning edit=1**

  Enter the new LUN zoning for host port 1.

  Enter the unique LUN mapping, as follows:

  G.l   GROUP.LUN number
  P     Place-holder
  R     Before GROUP.LUN to indicate Read-Only
  N     Clear current assignment
 ENTER  No change
  E     Exit command
  ?     Display detailed help text

External LUN 0: is not mapped.  Enter new internal LUN:

NOTE: `zoning edit` will cycle through each external LUN number even after you have passed the number of available internal LUNs / Tiers, to escape the loop type `e`

CAUTION: **Changes take effect immediately!**  If you accidentally change the mapping for an existing LUN it could cause data loss/corruption and will most-likely at least interrupt (offline) the SAN.  Changes should be done carefully.  Mistakes are best avoided by focusing on each line individually, the line will indicate current LUN mappings.  When in doubt strike the `ENTER` key or use `e` to escape and start over.

Next, run `tier verify=on` to allow the controller to continuously check the tier for disk related problems.

S2A 9900\[1\]: **tier verify=on**

                             Tier Status     

                         Space
             Capacity  Available     Disk
Tier Owner     (MiB)     (MiB)      Status                LUN List
-----------------------------------------------------------------------------
   1 +  1     4578344          0  ABCDEFGHPS  0 
   2 +  2     4578344          0  ABCDEFGHPS  1 
   3 +  1     4578344          0  ABCDEFGHPS  2 
   4 +  2     4578344          0  ABCDEFGHPS  3 
   5 +  1     4578344          0  ABCDEFGHPS  4 
   6 +  2     4578344          0  ABCDEFGHPS  5 
   7 +  1     4578344          0  ABCDEFGHPS  6 
   8 +  2     4578344          0  ABCDEFGHPS  7 
   9 +  1     4578344          0  ABCDEFGHPS  8 
  10 +  2     4578344          0  ABCDEFGHPS  9 
  11 +  1     4578344          0  ABCDEFGHPS  10 
  12 +  2     4578344          0  ABCDEFGHPS  11 
  13 +  1     4578344          0  ABCDEFGHPS  12 
  14 +  2     4578344          0  ABCDEFGHPS  13
  15 +  1     4578344          0  ABCDEFGHPS  14
  16 +  2     4578344          0  ABCDEFGHPS  15
  17 +  1     4578344          0  ABCDEFGHPS  16

                         Tier Verification Status:

 TIER   Scheduled   Continuous  Progress    Passes  Last Verify Completion Time  

-------------------------------------------------------------------------------

   1                    Yes       0.8%          29     16:17:07 01/22/2011 
   2        No          Yes                     29     16:17:05 01/22/2011 
   3                    Yes                     28     19:25:51 01/12/2011 
   4        No          Yes                     28     11:47:25 01/16/2011 
   5                    Yes                     28     09:35:10 01/22/2011 
   6        No          Yes                     27     09:46:24 01/22/2011 
   7                    Yes                     24     15:09:06 11/28/2010 
   8        No          Yes                     24     15:20:22 12/01/2010 
   9                    Yes                     24     09:21:15 12/04/2010 
  10        No          Yes                     24     15:10:33 12/12/2010 
  11                    Yes                     24     23:28:35 12/13/2010 
  12        No          Yes                     24     12:19:44 12/21/2010 
  13                    Yes                     48     22:39:50 12/30/2010 
  14        No          Yes                      0     12:46:02 01/05/2011 
  15                    Yes                      0     21:10:56 01/05/2011 
  16        No          Yes                      0     23:02:30 01/05/2011
  17                    Yes                      0     23:02:30 01/05/2011 

  System verify extent:    32 Mbytes
  System verify delay:     40
  Maximum active verifies: 2


Please enter a TIER ('a' for all TIERs owned by this unit or 'q' to quit): **a**

ALL valid TIERs owned by this unit selected

Do you want the verify to run continuously? (y/N): **y**

Next, run `cache writeback=on` and `av fastav=on`

S2A 9900\[1\]: **cache writeback=on**

                          Current Cache settings

           Write   Maximum   MF  Prefetch
    LUN   Caching  Prefetch  Bit  Ceiling
 ------------------------------------------------------------------------------
      0   Enabled    x    1   On   65535
      1   Enabled    x    1   On   65535
      2   Enabled    x    1   On   65535
      3   Enabled    x    1   On   65535
      4   Enabled    x    1   On   65535
      5   Enabled    x    1   On   65535
      6   Enabled    x    1   On   65535
      7   Enabled    x    1   On   65535
      8   Enabled    x    1   On   65535
      9   Enabled    x    1   On   65535
     10   Enabled    x    1   On   65535
     11   Enabled    x    1   On   65535
     12   Enabled    x    1   On   65535
     13   Enabled    x    1   On   65535
     14   Enabled    x    1   On   65535
     15   Enabled    x    1   On   65535
     16   Enabled    x    1   On   65535

Writeback Limit: 75%

                     2560.0 Mbytes of Cache Installed
                      (2048 Segments of 1024 Kbytes)

S2A 9900\[1\]: **av fastav=on**

           Current LUN Audio/Visual settings

                                Read     Write   Maximum
  LUN  Label          FastAV Continuous Caching  Prefetch
-----------------------------------------------------------
    0 SAS\_0              On      Off       On       x  1
    1 SAS\_1              On      Off       On       x  1
    2 SAS\_2              On      Off       On       x  1
    3 SAS\_3              On      Off       On       x  1
    4 SAS\_4              On      Off       On       x  1
    5 SAS\_5              On      Off       On       x  1
    6 SAS\_6              On      Off       On       x  1
    7 SAS\_7              On      Off       On       x  1
    8 SAS\_8              On      Off       On       x  1
    9 SAS\_9              On      Off       On       x  1
   10 SAS\_10             On      Off       On       x  1
   11 SAS\_11             On      Off       On       x  1
   12 SAS\_12             On      Off       On       x  1
   13 SAS\_13             On      Off       On       x  1
   14 SAS\_14             On      Off       On       x  1
   15 SAS\_15             On      Off       On       x  1
   16 SAS\_16             On      Off       On       x  1

  Disk Audio/Visual settings are:  Disabled  (Using disk defaults)
    Recovery Time Limit:           65535

  Ordered Tag Count:     0
  Unit Attention:        Enabled
  FASTAV Timeout:        200
  RC Timeout:            250
  Fail Check Condition:  Disabled