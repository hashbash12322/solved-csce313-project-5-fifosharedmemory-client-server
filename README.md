Download Link: https://assignmentchef.com/product/solved-csce313-project-5-fifosharedmemory-client-server
<br>
Two programming assignments PA2 and PA4 have made heavy use of the FIFORequestChannel class, which was pre-written and given to you along with the server code. If you did look at them, you may have noticed that the FIFORequestChannel class uses a mechanism called

“named pipes” or “FIFOs” to communicate between the two sides of the channel. However, FIFOs are only one of several different IPC mechanisms, each of which have their own particular uses that make them suited to particular applications. In this programming assignment, we are going to expand our toolbox by learning about 2 “new” IPC mechanisms in addition to named pipes: <em>message queues </em>and <em>shared memory</em>.

Background

Message Queues

While pipes provide a single unidirectional byte stream between two processes, message queues are slightly more sophisticated in the sense that they can be used between 2 or more processes. There are POSIX library functions as found in mq overview for message queue opening/creation, sending messages, receiving messages, closing the message queue, deleting the message queue, and modifying the message queue’s attributes. You may be able to use default attributes for this assignment. However, keep in mind that those defaults may vary by system. Visit the man pages for mq overview (note that this is POSIX IPC, not the System V IPC, which is the older way) for how to check and set default message queue attributes. In the end, you will have a class called MQRequestChannel which you can use in place of FIFOReqeustChannel such that all communication between the server and client is throught message queues instead of FIFO.

<h1>The Assignment</h1>

<h2>Code</h2>

You have to start off of your code from PA4. We are assuming that you have a working PA4.

If that is not the case, please contact us for a working version of PA4.

You then have to make up 3 versions (i.e., really just 3 modes of running the same code) of your PA5 each using a separate IPC-method-based request channels: FIFO, message queue, and shared memory. Call these versions PA5 FIFO, PA5 MQ, respectively. You should have an abstract RequestChannel class and 3 sub-classes:

FIFORequestChannel

1

MQRequestChannel

The API of base class RequestChannel should be as follows:

<table width="624">

 <tbody>

  <tr>

   <td width="624">class RequestChannel { public :/* some public constants */typedef enum {SERVER SIDE, CLIENT SIDE} Side ;protected :/* all IPC mechanisms will need a unique ‘‘name’’, so it is common to all */string my name;/* all channel will need to know which side it is */Side my side ; public :/* CONSTRUCTOR/DESTRUCTOR */RequestChannel (const string name , const Side side ){ my name = name , my side = side ;} virtual ˜RequestChannel (){}/* The following 2 pure virtual (i.e., =0 means pure) means they must be overridden in subclasses(e.g., FIFOReqeustChannel, MQRequestChannel) */virtual int cread (void* ptr , int len )=0; virtual int cwrite (void* ptr , int len )=0;;</td>

  </tr>

 </tbody>

</table>

}

Here, the first one FIFOReqeustChannel would be taken “almost” directly from PA4, except that you need to extend the base class RequestChannel and add required member variables. It must also override the cread and cwrite functions. The following code snipped shows it partially. Note that

<table width="624">

 <tbody>

  <tr>

   <td width="624">class FIFORequestChannel : public RequestChannel { // extends the base class private :/* the following are FIFO specific, because you know you are going to need 2 fd’s */ int rfd ;    // read pipe descriptor int wfd ;          // write pipe descriptorstring rfname ; // read pipe name string wfname ; // read pipe name public :/* CONSTRUCTOR/DESTRUCTOR */FIFORequestChannel (const string                   name , const Side           side );˜FIFORequestChannel ();int cread (char* ptr , int len ); int cwrite (char* ptr , int len );;</td>

  </tr>

 </tbody>

</table>

}

<h2>Compiling and Running Your Code</h2>

There should be only 1 makefile to compile everything together.

Take an additional runtime argument option “-i” which should get one of:

“f” for FIFO

“q” for message queue

The following is an example command to run PA5:

<table width="624">

 <tbody>

  <tr>

   <td width="624">./client -n &lt;requests/person&gt; -b &lt;bounded buffer size&gt;-w &lt;number of request channels&gt;-m &lt;buffer capacity&gt;-i &lt;f|q&gt;</td>

  </tr>

 </tbody>

</table>

You must resolve the derived type of RequestChannel class in the runtime using polymorphism and run-time binding in C++. That means that based on the value of argument “i”, you must choose what type of RequestChannel you are going to use. The following is block of code that show how to acheive that:

<table width="624">

 <tbody>

  <tr>

   <td width="624">RequestChannel* channel ;if ( i == “f”){ channel = new FIFOReqeustChannel (name, side );}else if ( i == “q”){ channel = new MQReqeustChannel (name, side );</td>

  </tr>

 </tbody>

</table>

}

<h2>Clean Up</h2>

You must clean up all IPC objects from the kernel memory and all temporary files you created. You can check the IPC objects currently persisting in your system by listing the /dev/mqueue directory or by running the ipcs command in shell. In addition, you should clean all heap-allocated objects.

<h2>Report</h2>

Gather timing data on the same set of <em>n,b,w,m </em>arguments on each of PA5 FIFO and PA5 MQ. Note that you need to modify the default capacity of the message queue to test out different <em>m </em>values that are larger than the default.

Present a performance comparison of the different IPC mechanisms based on this data, and attempt to provide explanation for any differences and similarities.

Present the results in separate graphs using PA5 FIFO (i.e., PA4) as the baseline for comparison.

What is the maximum <em>w </em>and thus the max number of RequestChannels that you can use for each IPC? How much more can you go beyond the limit in PA4? (recall that the limit imposed by how many file descriptors each user can have.

What are some of the limits encountered by each class, either due to the specific implementation or to operating system limitations, and how does the program behave when it encounters them?

Describe the clean-up activity you have done for each IPC