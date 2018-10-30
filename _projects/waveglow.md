#WaveGlow: a Flow-based Generative Network for Speech Synthesis

**Ryan Prenger, Rafael Valle and Bryan Catanzaro**

In our recent [paper], we propose WaveGlow: a flow-based network capable
of generating high quality speech from mel-spectrograms. WaveGlow
combines insights from [Glow] and [WaveNet] in order to provide fast,
efficient and high-quality audio synthesis, without the need for
auto-regression. WaveGlow is implemented using only a single network,
trained using only a single cost function: maximizing the likelihood of
the training data, which makes the training procedure simple and
stable.\

Our [PyTorch] implementation produces audio samples at a rate of more than
500 kHz on an NVIDIA V100 GPU and Mean Opinion Scores show that it delivers
audio quality as good as the best publicly available WaveNet
implementation. 

Below we provide real and synthesized samples using our WaveGlow model, Griffim-Lim and an open source
[WaveNet implementation].

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
    <tr>
        <td>
            <iframe 
                frameborder="0" 
                width="256"   
                height="64"
                <iframe src="https://drive.google.com/embeddedfolderview?id=1w9H0Lj3Pn3kbxmliwg-rm7lgFa8LjoOB#list" style="width:100%; height:600px; border:0;"></iframe>
        </td>

        <td>
            <iframe 
                frameborder="0" 
                width="256"   
                height="64"
                <iframe src="https://drive.google.com/embeddedfolderview?id=1wrYGZCGpSYWp2uEuPq1TvB9UIgviJ3Qy#list" style="width:100%; height:600px; border:0;"></iframe>
        </td>
        <td>
            <iframe 
                frameborder="0" 
                width="256"   
                height="64"
                <iframe src="https://drive.google.com/embeddedfolderview?id=1X6i3yOdV7_iJF2iSXRky57eGNX92n2wm#list" style="width:100%; height:600px; border:0;"></iframe>
        </td>
        <td>
            <iframe 
                frameborder="0" 
                width="256"   
                height="64"
                <iframe src="https://drive.google.com/embeddedfolderview?id=1NONEXso_D7tDJEKwb0RvsrhTF0n8EemN#list" style="width:100%; height:600px; border:0;"></iframe>
        </td>
    </tr>
</table>