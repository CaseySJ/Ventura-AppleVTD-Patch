# Ventura-AppleVTD-Patch
Patch for macOS 13.3+ to fix WiFi and Ethernet issues when AppleVTD is enabled and system has (a) >16GB memory and (b) supported iGPU enabled

| Date               | Notice |
| :--                | :-- |
| 06 Apr 2023        | This patch has been merged into OpenCore as a new kernel quirk named `DisableIoMapperMapping`. It will be available in May with OpenCore 0.9.2. Details of the quirk can be [found here](https://github.com/acidanthera/OpenCorePkg/pull/440). When the quirk is available and enabled, the patches described below should be disabled or deleted. |


**Problem:**

On some systems such as Z390 Designare and Z490 Vision D, WiFi and Ethernet stopped working with macOS Ventura 13.3. This problem affects all motherboards whose native DMA remap table (DMAR) contains one or more *Reserved Memory Regions*. Further, this problem occurs only when AppleVTD is enabled and more than 16GB memory is installed.


**Solution:**

- You may [re-enable AppleVTD](https://www.tonymacx86.com/threads/success-gigabyte-designare-z390-thunderbolt-3-i7-9700k-amd-rx-580.316533/post-2262129) if you disabled it
- Apply the following patch to *config.plist -> Kernel -> patch*

An explanation of how the patch was found and what it does is [provided here](https://www.tonymacx86.com/threads/success-gigabyte-designare-z390-thunderbolt-3-i7-9700k-amd-rx-580.316533/post-2365883).

**Patch Details:**

<table>
<tbody>
<tr>
<td>Identifier</th>
<td>com.apple.iokit.IOPCIFamily</th>
</tr>
<tr>
<td>Base</td>
<td>__ZN11IOPCIBridge20addBridgeMemoryRangeEyyb</td>
</tr>
<tr>
<td>Comment</td>
<td>CaseySJ - Fix AppleVTD issue in 13.3+</td>
</tr>
<tr>
<td>Find</td>
<td>4C89F6E8 9AFF0000</td>
</tr>
<tr>
<td>Replace</td>
<td>4C89F690 90909090</td>
</tr>
<tr>
<td>MinKernel</td>
<td>22.4.0</td>
</tr>
<tr>
<td>Count</td>
<td>1</td>
</tr>
<tr>
<td>Enabled</td>
<td>True</td>
</tr>
</tbody>
</table>


This patch can be added manually by copying-and-pasting into *config.plist*. It can also be inserted automatically using *OpenCore Configurator*[^1] as follows:

1. Download patch by clicking green button on upper right of this webpage
2. Mount EFI partition, launch *OpenCore Configurator*, open your *config.plist*
3. In *OpenCore Configurator* navigate to `Kernel -> Patch` as shown
4. Right-click in the white and gray area to show pop-up menu 
5. Choose `Import set of patches` and select the file `CaseySJ-macOS-Ventura-AppleVTD-Patch.plist`

![Screenshot 2023-04-02 at 2 44 04 PM](https://user-images.githubusercontent.com/48335376/229380690-095ec4b8-1687-4dba-83ba-7f17ff050873.png)

The patch will be added to the end of your current set of patches (if any):
![Screenshot 2023-04-02 at 2 46 21 PM](https://user-images.githubusercontent.com/48335376/229380708-bec893c7-1673-4da5-a743-17f14701e5ce.png)

[^1]: **Warning:** Always use the proper version of *OpenCore Configurator (OCC)*! Each version of OCC supports two versions of OpenCore. Check the OCC *title bar* (see screenshot above) to see which version of OpenCore is being supported. Do **not** use a version of OCC that does not support your version of OpenCore! Also make sure that only **one EFI partition is mounted** when using *OpenCore Configurator*.
