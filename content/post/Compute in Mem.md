
---
title: "Compute in memory workshop notes"
---

# Challenge

# Aims of this Tutorial
## Computing challenges in deep learning at the edge: Real-time requirements 

# AI hw
## What where when why
	Efficiency gap:
	- biological systems still possses a level of functionality that is unmatched artifical systems
	- battery life is about on hour given no display or other things 
	- operational energy use of AI is very high in the range tera watts for only 5 queries for 2B users
	- most applications use matrix vector multiplications -> therefore we try to accelerate it
	- softmaxs are bottle necks 
		- Solns -> Circuits & archs that can efficiently implement the algos(possibly embody computing principles from the brain)
			- near / in-mem computing -> data dev costs
			- approx(quatization) & static hw
			- neuromorphic devices & interconnect
			- event driven computation


### Neuro-mimetic devices
	- RRAM
	- PCM
	- MTJ
	- CMOS(SRAM)

# CiM, RiM
	CiM in 6t cell can short if not done correctly
	Add ops n others can be done using analog computations
	ROM & RAM simulataneously
	Where to CiM
		Tram, sram 
	What
		- Analog vs digital computing
	When
		- Depends on applications
- the above are not co-dependent
### Adaptive SNR 

### Reducing ADC overhead
- Train Dnn model with partial-sum quantization in the training loop.
