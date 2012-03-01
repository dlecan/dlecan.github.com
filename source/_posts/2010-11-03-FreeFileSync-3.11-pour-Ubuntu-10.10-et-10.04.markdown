--- 
layout: post
title: FreeFileSync 3.11 pour Ubuntu 10.10 et 10.04
categories: 
- FreeFileSync
permalink: /archives/66-FreeFileSync-3.11-pour-Ubuntu-10.10-et-10.04.html
s9y_link: http://www.dlecan.com/archives/66-FreeFileSync-3.11-pour-Ubuntu-10.10-et-10.04.html
date: 2010-11-03 08:03:34 +01:00
---
<p><a href="http://www.dlecan.com/archives/65-Des-nouvelles-de-FreeFileSync-3.9.html">Contrairement à ce que j'indiquais dans mon précédent billet</a>, FreeFileSync est finalement disponible pour Ubuntu 10.10 comme pour Ubuntu 10.04.</p> <br />
<p>En effet, j'ai réussi à <em>backporter</em> la librairie boost 1.44 disponible en <em>Debian experimental</em> pour Ubuntu 10.04 et 10.10. Combiné à une évolution du système de build introduite par ZenJu dans les sources de FreeFileSync et qui cassait le build du package, il m'a fallu plusieurs semaines pour réussir à contourner tous ces problèmes.</p> <br />
<p>Pendant ce temps, FFS 3.10 est sorti, puis FFS 3.11 et c'est cette version qui se trouve aujourd'hui dans le <a href="https://launchpad.net/~freefilesync/+archive/ffs">dépôt PPA du projet</a>.</p> <br />
<p>Voici les <em>changelogs</em> :</p> <br />
<ol> <br />
<li>FFS 3.10<br /> <br />
<ul> <br />
<li>Automatically solve daylight saving time and time zone shift issues on FAT/FAT32 (finally)&#160;</li> <br />
<li>Instantly resolve abandoned directory locks associated with local computer&#160;</li> <br />
<li>Show expanded directory name as tooltip and label text (resolves macros and relative paths)&#160;</li> <br />
<li>Do not copy relative file attributes for base target directories that are created implicitly&#160;</li> <br />
<li>Move dialogs by clicking (almost) anywhere&#160;</li> <br />
<li>RealtimeSync: ignore request for device removal on Samba shares&#160;</li> <br />
<li>Added UTF-8 BOM for CSV export&#160;</li> <br />
<li>Correctly handle window position on multi-screen desktop&#160;</li> <br />
<li>Disabled warning &quot;database not yet existing&quot;&#160;</li> <br />
<li>RealtimeSync: replaced delay by minimum idle time&#160;</li> <br />
<li>Maximum number of folder pairs configurable via GlobalSettings.xml (XML node &lt;FolderPairsMax&gt;)&#160;</li> <br />
<li>Added tooltips to display long filenames on main grid&#160;</li> <br />
<li>Keep application responsive when deleting large directories&#160;</li> <br />
<li>Vista/Windows 7: harmonize modification times shown on main grid with Windows Explorer <br /></li> <br />
<li>Changed background color to avoid unreadable texts in combination with certain color themes&#160;</li> <br />
<li>Toggle middle grid comparison result/sync preview with right mouse button click&#160;</li> <br />
<li>Further GUI enhancements/polishment/standard conformance&#160;</li> <br />
<li>Updated translation files<br /></li> <br />
</ul> <br />
</li> <br />
<li>FFS 3.11<br /> <br />
<ul> <br />
<li>Fixed migration issue: reasonable default value for number of folder pairs<br />
</li> <br />
<li>Better message box background color<br /></li> <br />
</ul> <br />
</li> <br />
</ol><br />
