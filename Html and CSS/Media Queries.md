- reduce the use of media queries as it creates the maintenance hell. rather use max, min or any other CSS features and use media queries as less as possible

### Media-Types
- **all** Used for all devices
- **print** Used for printing devices
- **screen** Used for computers, tablets and mobile devices
- **speech** Used for devices using assistive technologies(screen reader)

### Where you can provide value for
- Width
- Height
- Device-width
- Device-height
- Scan
- Monochrome
- Resolution
- Grid
- Color
- Color-index
- Aspect-ratio
- Device-aspect-ratio
- Orientation

### **Syntax**
```
@media screen and (min-width: 480px) {

}

@media screen and (min-width: 480px) and (max-width: 800px) {

}

@media screen and (min-width: 480px) and (max-width: 800px), (min-width: 1100px) {

}

Portrait
@media screen and (orientation:portrait)

 Landscape
@media screen and (orientation:landscape)


```