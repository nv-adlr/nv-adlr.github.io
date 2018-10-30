##WaveGlow: a Flow-based Generative Network for Speech Synthesis

**Ryan Prenger, Rafael Valle and Bryan Catanzaro**

In our recent [paper], we propose WaveGlow: a flow-based network capable
of generating high quality speech from mel-spectrograms. WaveGlow
combines insights from [Glow] and [WaveNet] in order to provide fast,
efficient and high-quality audio synthesis, without the need for
auto-regression. WaveGlow is implemented using only a single network,
trained using only a single cost function: maximizing the likelihood of
the training data, which makes the training procedure simple and
stable.

Our [PyTorch] implementation produces audio samples at a rate of more than
500 kHz on an NVIDIA V100 GPU and Mean Opinion Scores show that it delivers
audio quality as good as the best publicly available WaveNet
implementation. 

Below we provide real samples and synthesized samples using our WaveGlow model, Griffim-Lim 
and an open source [WaveNet implementation].

[paper]: arxiv.org
[WaveNet implementation]: https://github.com/r9y9/wavenet_vocoder
[Glow]: https://blog.openai.com/glow/
[WaveNet]: https://deepmind.com/blog/wavenet-generative-model-raw-audio/
[PyTorch]: http://pytorch.org

<table border="1" width="100%" style="font-size:16px">
<tr>
    <th bgcolor="#3cb371" height="48"> Griffin-Lim </th>
    <th bgcolor="#3cb371"> WaveNet </th>
    <th bgcolor="#3cb371"> WaveGlow </th>
    <th bgcolor="#3cb371"> Real Audio </th>
</tr>
<tr>
	<td>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1_HPhlReS6bsVUSSbOh1pt2qHZb3woRhu" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1jj4D9yTrDvfszdXV0J7AQwH_DQoUez64" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1qcE0Enl__yRxLMP9VG961mT2cCpeLSL3" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1y8peLhggV58-e3beCtg8PmVAvkrquDrv" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1Vh9IMTVvHF5nCLEkb_Yx_BfIbtG1MZkH" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1OayRRsVyVMXqHx_SqH-cMvszLpITRpkB" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1oYK1lDtoIa5I3YTbhU-_ejRAb1llDZLm" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1fliavy2xcIUiVhwA7kizDyMntBgWL4dX" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1R3S_4TqsLy8e0pOKb0curUOlALy6KSXH" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"><source src="http://docs.google.com/uc?export=open&id=1MIsvKk9FdSYX-M65i2GhLJl8dK6_cN-2" type="audio/mpeg">audio not supported</audio>
	</td>		
	<td>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1_p_PapklkhQvEVcXm4U_XrEXRp4rNK_o" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1nDpkDHDwM4QnSfZoEbh_kYHGqWWQNSMz" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1nAL6N93J_saV2GzyCGCcJAnSkbIZJ-KW" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1U3iNNGiqOW0C0e1Qnnd971hWP3DGzKrJ" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1AhI7ANQCpJu1hTYJEzJV4MiEQKFg8zs7" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1FxXiUnZHpg7PIwUag4ng8cRUtGBQyLK5" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=15DgNatGh7FDemZfJQfS0_sM2M3W64FiJ" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1BpHk_uZ5Id7gJEyJz8EtRDPSykRl2toT" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1Kekw4pUZ7HafSsBqHvVOQ9YumnhVsrdS" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=11Gm9_o2XFvsV-Ot3mbLvTACTXuk9VrgX" type="audio/mpeg">audio not supported</audio>
	</td>		
	<td>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1t1f8LVvcbBfW-NEhBSSIufUuq3nc6ORd" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1bXdX40rzU8-Cde36cLdqPZVSQn1uJ-mv" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1_lqKW1gHJhKh_PUF2kqmv8l4-m2gIk5U" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1NA6rxKt9o_fFF0N7B9sIf-Kf9qvsBeLJ" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1A9pC0aW377bBBrzsKsOdxO8IUWUdFxQb" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1yr3MSAFov4Qgiowzi6NbBGRkZVmjph-r" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1V8rvtB-eTZRX1ZttaKOEmJ7QCOwg3YxE" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1LjZfPaBVWbsLph0CCKRqTNUFBcycGWH0" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1WU1hTQ93I8ZvIGSmwz3zlxhLNR2Eo8QQ" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1rTLGqqPRR4ETarcTYlkd0iM4WSqIYBDe" type="audio/mpeg">audio not supported</audio>
	</td>
	<td>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1Ppxcis8U-LwYx-VqMlB_pcqUGmDVZzds" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1z4cs0J5IGHaCu40YsQwwkePT6H895RH4" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1tA4s5UeX-BwLVe3J-TzsufeA0-gbvwpY" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1Lzy2fxs9nxX-_6_EdDOF5mqvgBUlrmPD" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1_abpUXI7NEG9G-oz_2wNXnoCwAfrpB_V" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1pT2D0Ov2z0gva7e7gBM4KwJTAAE-nRQI" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1QihoCcA0XSb7f-15GUzcKi0dNNYLzuvZ" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1H8m9rBmUdLcAsDm0vs8uUgX31CVUWsU2" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1EvlIpz0VXZiYdwe36xD3l4qXrxW0TWP1" type="audio/mpeg">audio not supported</audio>
		<audio controls preload="none" style="width: 200px"<source src="http://docs.google.com/uc?export=open&id=1kM_7q5dVGkf4CV97cc7rY07JLwB9VaAL" type="audio/mpeg">audio not supported</audio>	</td>
</tr>
</table>