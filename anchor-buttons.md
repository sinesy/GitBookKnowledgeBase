---
description: Since 6.0.2
---

# Anchor buttons

if you have a large window with many panels and you want to create links to see them quickly you may need to create anchors button.

1. Add a **buttons panel** to the window

![](<.gitbook/assets/image (19).png>)

2\. Select the panel where you want to create a link and select the "**Create anchor button**" menu&#x20;

![](<.gitbook/assets/image (11) (1).png>)

3\. Now you can define the anchor button. Select the **container** for the anchor button; type the label of button and select the container panel which is scrollable.&#x20;

![](<.gitbook/assets/image (12).png>)

4\. Now you have an anchor button in button panel&#x20;

![](<.gitbook/assets/image (16).png>)

5\. That's all: now you can use the anchor button in the interpreted application.



You can customize the button action but you can't change the references of the link panel and the scrollable panel. This information must be the same used for the style and must refer the panels included in the window. If you want to change these references, you'd better cancel the anchor and recreate it.

![](<.gitbook/assets/image (13).png>)

If needed, you can select a javascript action to execute when the scroll activates the anchor button.

![](<.gitbook/assets/image (17).png>)

When the anchor button is selected, the following default CSS class is applied:

```css
.x-anchor-btn-selected {
	background: #50A3ED !important;
}

.x-anchor-btn-selected button {
	color: white !important;
}
```
