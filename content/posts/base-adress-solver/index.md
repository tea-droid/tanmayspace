---
title: "Understanding FirmXRay and Base Address Solver Java file "
author: "Tanmay Sharma"
date: 2025-07-02
---
## FirmXRay
link : https://github.com/OSUSecLab/FirmXRay

A static analysis tool based on Ghidra to detect Bluetooth link layer vulnerabilities from bare-metal firmware. As proof-of-concept, the current implementation supports firmware (ARM Cortex-M Architecture) developed based on Nordic and TI SDKs (i.e., SoftDevice and BLE-Stack).

The three main components of FirmXRay are:
1. Base address recognition. It can automatically infer the firmware base address using the point-to relation heuristics. The output result will be in ./base/base.txt.
2. Backward slicing. FirmXRay will start from the SDK APIs and backward extract the relevant program paths.
3. Static value computation. FirmXRay can statically execute ARM instructions to compute the configuration values from the program slices.

For more details, please refer to our paper FirmXRay: Detecting Bluetooth Link Layer Vulnerabilities From Bare-Metal Firmware.

## Base Address Solver
+ location on the repository:  /src/core/Baseaddresssolver.java
+ The BaseAddressSolver is responsible for guessing the base memory address of a firmware binary by analyzing absolute address references, function entry points, string pointers, and interrupt vector tables.
+ essential where there is not metadata
+ runs in a headless ghidra environment


### Code Analysis 
+ First the code sets up the ghidra application in headless mode.
```java
if (!Application.isInitialized()) {                                                                           # checks if Ghidra has already been initialized in this runtime session.
	ApplicationConfiguration configuration = new HeadlessGhidraApplicationConfiguration();                     # tells Ghidra not to load the UI components.
	configuration.setInitializeLogging(false);                                                                # default log o/p to avaoid clutter 
	Application.initializeApplication(new GhidraJarApplicationLayout(), configuration);                        # loads the entirte ghidra backend such as, program database, language providers, memory models
}
```
  - this step basically sets up the environment for other ghidra API

+ then it loads the firmware binary into Ghidra
```java
GhidraProject ghidraProject;                                                                                  # a container for all programs being analyzed.
TestProgramManager programManager = new TestProgramManager();                                                 # a test utility wrapper to help manage program loading in scripted analysis — even though it's named "Test," it's used here to simplify scripting.							

ghidraProject = GhidraProject.createProject(projectDirectoryName, projectName, true);                         # creates a new Ghidra project directory.
File file = new File(programName);
if (!file.exists()) {
	throw new FileNotFoundException("Can not find program: " + programName);                                  # this part is to check if the firmware binary exists, if not → throws exception to prevent further processing
}
```

+ Sets disassembly architecture(ARM) using a language provider. Ghidra uses 'sleigh' as its architecture specific language
```java
LanguageProvider languageProvider;
try {
	languageProvider = new SleighLanguageProvider();                                                          # sleigh gives the ability to select an instruction set arch (ISA) like ARM etc.
} catch (Exception e) {
	System.out.println("Unable to build language provider.");                                                 # if failed, msg is printed and exits
	return 0;
}
```

+ 
```java
int txId = program.startTransaction("Analysis");                                                              # starts a transaction
AutoAnalysisManager mgr = AutoAnalysisManager.getAnalysisManager(program);                                    # AutoAnalysisManager : Discovers function boundaries, Marks control flow, Analyzes pointers, strings, etc.
mgr.initializeOptions();
mgr.reAnalyzeAll(null);
mgr.startAnalysis(TimeoutTaskMonitor.timeoutIn(Constant.DISASSEMBLE_TIMEOUT, TimeUnit.SECONDS));              #  run with a timeout using TimeoutTaskMonitor to avoid infinite loops or huge binaries from locking up the process.
```
  - transaction means multiple modifications to the program (e.g., marking code, adding references) are grouped together.

+ It applies various constraints and this is the core logic where candidate base addresses are calculated.	
  - it has 4 constraints
    -  Constraint 1: Base Range Estimation (from LDRs)
    - Constraint 2: Function Prologue Matching
    - Constraint 3: String Pointer Matching
    - Constraint 4: Interrupt Vector Table (for Nordic MCUs)
		
```java
List<Long> LDRset = FunctionUtil.findAllImmediateInLDR(program);                                              # contains absolute memory addresses?

for (int i = 0; i < LDRset.size() - 1; ++i) {
	long diff = LDRset.get(i + 1) - LDRset.get(i);
	if (diff > Constant.THRESHOLD) {
		low = LDRset.get(i) - size;
		if (low < 0) low = 0;

		if (high < Constant.MAX_BASE && high > Constant.MIN_BASE) {
			...
			lowerBound.add(low);
			upperBound.add(high);
		}
		high = LDRset.get(i + 1);
	}
}

List<Long> AbsAddrSet = FunctionUtil.findAllImmediateInLDR(program);
List<Long> entry = BaseAddressUtil.getFunctionEntries(program);

for (long ab : AbsAddrSet) {
	...
	for (long start : entry) {
		long candidate = ab - start;
		...
		addScore(scores, candidate);
	}
}
```
  - the idea here is that a LDR instruction might be loading the absolute address of a function.

+ constraits are a bit hard to understand

+ Scoring and Ranking

```java
scores = NumUtil.sortByValues(scores, true);
...
while (iterator.hasNext()) {
	long candidate = iterator.next();
	int constraintIndex = checkConstraint(candidate, range);
	if (constraintIndex != -1) {
		base = candidate;
		...
		break;
	}
}
```
  - All candidate base addresses are sorted by how many constraints they match (i.e., how many times addScore() was called)
  - The top-scoring candidates are tested to ensure they fall inside one of the valid ranges (Constraint 1).
  - The best candidate is returned.

```java
return base;
```
  - This base is now used by other modules (STRInsSolver, MemoryMapper, ExecutionPathFinder) to resolve absolute memory addresses accurately.
