---
title: JavaScript与Google Earth的纠结生活
id: 4
date: 2010-03-20 09:03:19
tags:
---

<script src="http://www.google.com/jsapi?key=ABCDEFG"> </script>
   <script type="text/javascript">
      var ge;
      google.load("earth", "1");

      function init() {
         google.earth.createInstance('map3d', initCB, failureCB);
      }

      function initCB(instance) {
         ge = instance;
         ge.getWindow().setVisibility(true);
      }

      function failureCB(errorCode) {
      }

      google.setOnLoadCallback(init);
   </script>