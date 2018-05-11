# mpvue-map-bug-report
bugs report for mpvue map

地图存在以下问题：  1.绑定了end 和 start 方法之后，分别在end 方法 和 start 方法对markers数组改变，会导致地图刷新存在卡顿情况
                 2.请查看代码中另一个已注释的代码setMarker,若在该处执行setMarker的操作，虽然在模拟器上能够流畅运行，但是在真机 iphone 6p中依然会存在卡顿现象
