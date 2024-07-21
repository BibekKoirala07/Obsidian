## Html

### input
attribute : `max maxlength min pattern readonly required size step value disabled multiple placeholder autofocus autocompelete`

- `button color data datetime-local hidden file image month number password range reset search tel time url week email`

```
<form onSubmit={} action="" target="" method="" novalidate autocomplete="on" id="form">

	<input type="text" id="" name="" value="" /> /* default character=20 */
	
	// can't be selected more than one with same name(property)
	<input type="radio" value="" name="" />
	
	// can be selected more than on same same
	<input type="checkbox" name="" value="" />
	
	<input type="submit" name="" value="" />
	
	<input type="button" value="" />

	<input type="date" min="" max="" />

	<input type='range' min="" max="" step="" />

	<input type="tel" pattern="[0-9]{3}-[0-9]{2}-[0-9]{3}"

</form>

<input type="text" form="from1" /> // now this input belongs to form above and data will be sent together to server if we submit
```

### Text-Area

### **Select**
```
<form>
	<label for="cars">Choose: </label>
	<select id="" name="">
		<option value="">Option</option>
		<option value="audi">Audi</option>  
	</select>

	// have option group
	<optgroup>
		<option>
		<option>
	</optgroup>

	// Have a selected value at first
	<option value="" selected></option>

	//to have number of visible options at first
	<select size="3" > </select>

	// to select the multiple options
	<select multiple></select>
</form>
```

### **Field-Set**: 
```
<form >  
  <fieldset>  
    <legend>Personalia:</legend>  
    <label for="fname">First name:</label><br>  
    <input type="text" id="fname" name="fname" value="John"><br>  
    <input type="submit" value="Submit">  
  </fieldset>  
</form>
```
![[Pasted image 20231023164652.png]]

### **Data-list** :
```
<form action="/action_page.php">  
  <input list="browsers">  
  <datalist id="browsers">  
    <option value="Edge">  
    <option value="Firefox">  
    <option value="Chrome">  
    <option value="Opera">  
    <option value="Safari">  
  </datalist>  
</form>
```
![[Pasted image 20231023164805.png]]


### Output

# caret color

