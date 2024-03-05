	
## BAClear V.s JEClear
	
https://stackoverflow.com/questions/31280817/what-branch-misprediction-does-the-branch-target-buffer-detect/65796517#65796517
	
I am currently looking at the various parts of the CPU pipeline which can detect branch mispredictions. I have found these are:

Branch Target Buffer (BPU CLEAR)
Branch Address Calculator (BA CLEAR)
Jump Execution Unit (not sure of the signal name here??)
I know what 2 and 3 detect, but I do not understand what misprediction is detected within the BTB. The BAC detects where the BTB has erroneously predicted a branch for a non-branch instruction, where the BTB has failed to detect a branch, or the BTB has mispredicted the target address for a x86 RET instruction. The execution unit evaluates the branch and determines if it was correct.

What type of misprediction is detected at the Branch Target Buffer? What exactly is detected as a misprediction here?
18

This is a good question! I think the confusion that it's causing is due to Intel's strange naming schemes which often overload terms standard in academia. I will try to both answer your question and also clear up the confusion I see in the comments.

First of all. I agree that in standard computer science terminology a branch target buffer isn't synonymous with branch predictor. However in Intel terminology the Branch Target Buffer (BTB) [in capitals] is something specific and contains both a predictor and a Branch Target Buffer Cache (BTBC) which is just a table of branch instructions and their targets on a taken outcome. This BTBC is what most people understand as a branch target buffer [lower case]. So what is the Branch Address Calculator (BAC) and why do we need it if we have a BTB?

So, you understand that modern processors are split into pipelines with multiple stages. Whether this is a simple pipelined processor or an out of order supersclar processor, the first stages are typically fetch then decode. In the fetch stage all we have is the address of the current instruction contained in the program counter (PC). We use the PC to load bytes from memory and send them to the decode stage. In most cases we increment the PC in order to load the subsequent instruction(s) but in other cases we process a control flow instruction which can modify the contents of the PC completely.

The purpose of the BTB is to guess if the address in the PC points to a branch instruction, and if so, what should the next address in the PC be? That's fine, we can use a predictor for conditional branches and the BTBC for the next address. If the prediction was right, that's great! If the prediction was wrong, what then? If the BTB is the only unit we have then we would have to wait until the branch reaches the issue/execute stage of the pipeline. We would have to flush the pipeline and start again. But not every situation needs to be resolved so late. This is where the Branch Address Calculator (BAC) comes in.

The BTB is used in the fetch stage of the pipeline but the BAC resides in the decode stage. Once the instruction we fetched is decoded, we actually have a lot more information which can be useful. The first new piece of information we know is: "is the instruction I fetched actually a branch?" In the fetch stage we have no idea and the BTB can only guess, but in the decode stage we know it for sure. It is possible that the BTB predicts a branch when in fact the instruction is not a branch; in this case the BAC will halt the fetch unit, fix the BTB, and reinitiate fetching correctly.

What about branches like unconditional relative and call? These can be validated at the decode stage. The BAC will check the BTB, see if there are entries in the BTBC and set the predictor to always predict taken. For conditional branches, the BAC cannot confirm if they are taken/not-taken yet, but it can at least validate the predicted address and correct the BTB in the event of a bad address prediction. Sometimes the BTB won't identify/predict a branch at all. The BAC needs to correct this and give the BTB new information about this instruction. Since the BAC doesn't have a conditional predictor of its own, it uses a simple mechanism (backwards branches taken, forward branches not taken).

Somebody will need to confirm my understanding of these hardware counters, but I believe they mean the following:

BACLEAR.CLEAR is incremented when the BTB in fetch does a bad job and the BAC in decode can fix it.
BPU_CLEARS.EARLY is incremented when fetch decides (incorrectly) to load the next instruction before the BTB predicts that it should actually load from the taken path instead. This is because the BTB requires multiple cycles and fetch uses that time to speculatively load a consecutive block of instructions. This can be due to Intel using two BTBs, one quick and the other slower but more accurate. It takes more cycles to get a better prediction.
This explains why the penalty of a detecting a misprediction in the BTB is 2/3 cycles whereas the detecting a misprediction in the BAC is 8 cycles.	

	
	
## Cache
https://stackoverflow.com/questions/4666728/why-is-the-size-of-l1-cache-smaller-than-that-of-the-l2-cache-in-most-of-the-pro/38549736#38549736
	
## 电容与电感
https://blog.csdn.net/weixin_38233274/article/details/80179197
动态元件：电路中某些元件的参数（比如电压、电流）其约束关系是通过导数或积分来表达的，这些元件就称为动态元件。 

静态元件：电路中某些元件的参数（比如电阻）其约束关系不是通过导数或积分来表达的，这些元件就称为静态元件。

1、简单的说，动态原件就是电路中某些参数的约束关系是通过导数或积分来表达的,所以称为动态元件. 即用微分或微分来表电路中的u~i关系。

2、比如电容两端的电压和通过电感的电等，i(t)=c*du(t)/dt,u(t)=L*di(t)/dt,电流是通过电压的变化得到了，同理电压是通过电流的变化得到的，所以称为动态元件

3、电阻就是很好的静态元件

电容器是由两块金属极板，中间隔以绝缘介质（如空气、云母、绝缘纸、电解质等）组成，当电容器的两块金属极板之间加以电压时，两块极板上就会聚集等量异性的电荷（ charge ），从而建立起电场，储存电场能量，当外加电压撤掉后，极板上的电荷可继续存在，因此，电容器是一种能储存电荷的元件。但是，实际的电容器由于存在介质损耗和漏电流，极板上的电荷会慢慢地消失，时间越长，电荷越少
	
电容上的电流与电压呈微分关系，即任一时刻电容上的电流取决于该时刻电压的变化率，而与该时刻电压本身无关。电压变化越快，电流也就越大，即使某时刻的电压为 0 ，也可能有电流；如果电容两端电压为直流电压（ DC voltage ），即电压不随时间的变化而变化，那么电容上就无电流通过，这时电容相当于开路，所以，电容具有隔直流作用。	。	

