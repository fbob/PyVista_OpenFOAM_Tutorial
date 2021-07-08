```python
import pyvista as pv
import numpy as np
import matplotlib.pyplot as plt
import vtk as vtk
```


```python
%matplotlib widget
```

# Try to load case with `pyvista.read`


```python
# Import the OpenFOAM example case
case = pv.read('./case.foam')
```


```python
mesh = case[0]
```


```python
mesh
```




<table><tr><th>Header</th><th>Data Arrays</th></tr><tr><td>
<table>
<tr><th>UnstructuredGrid</th><th>Information</th></tr>
<tr><td>N Cells</td><td>12225</td></tr>
<tr><td>N Points</td><td>25012</td></tr>
<tr><td>X Bounds</td><td>-2.060e-02, 2.900e-01</td></tr>
<tr><td>Y Bounds</td><td>-2.540e-02, 2.540e-02</td></tr>
<tr><td>Z Bounds</td><td>-5.000e-04, 5.000e-04</td></tr>
<tr><td>N Arrays</td><td>26</td></tr>
</table>

</td><td>
<table>
<tr><th>Name</th><th>Field</th><th>Type</th><th>N Comp</th><th>Min</th><th>Max</th></tr>
<tr><td><b>C</b></td><td>Points</td><td>float32</td><td>3</td><td>-2.540e-02</td><td>2.900e-01</td></tr>
<tr><td>Cx</td><td>Points</td><td>float32</td><td>1</td><td>-2.060e-02</td><td>2.900e-01</td></tr>
<tr><td>Cy</td><td>Points</td><td>float32</td><td>1</td><td>-2.540e-02</td><td>2.540e-02</td></tr>
<tr><td>Cz</td><td>Points</td><td>float32</td><td>1</td><td>-4.313e-20</td><td>5.252e-20</td></tr>
<tr><td>U</td><td>Points</td><td>float32</td><td>3</td><td>0.000e+00</td><td>1.000e+01</td></tr>
<tr><td>epsilon</td><td>Points</td><td>float32</td><td>1</td><td>1.485e+01</td><td>1.485e+01</td></tr>
<tr><td>k</td><td>Points</td><td>float32</td><td>1</td><td>3.750e-01</td><td>3.750e-01</td></tr>
<tr><td>nuTilda</td><td>Points</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>nut</td><td>Points</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>omega</td><td>Points</td><td>float32</td><td>1</td><td>4.401e+02</td><td>4.401e+02</td></tr>
<tr><td>p</td><td>Points</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>v2</td><td>Points</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>wallShearStress</td><td>Points</td><td>float32</td><td>3</td><td>-2.106e-02</td><td>2.106e-02</td></tr>
<tr><td><b>C</b></td><td>Cells</td><td>float32</td><td>3</td><td>-2.493e-02</td><td>2.874e-01</td></tr>
<tr><td>Cx</td><td>Cells</td><td>float32</td><td>1</td><td>-1.981e-02</td><td>2.874e-01</td></tr>
<tr><td>Cy</td><td>Cells</td><td>float32</td><td>1</td><td>-2.493e-02</td><td>2.525e-02</td></tr>
<tr><td>Cz</td><td>Cells</td><td>float32</td><td>1</td><td>-7.640e-20</td><td>8.296e-20</td></tr>
<tr><td>U</td><td>Cells</td><td>float32</td><td>3</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>epsilon</td><td>Cells</td><td>float32</td><td>1</td><td>1.485e+01</td><td>1.485e+01</td></tr>
<tr><td>k</td><td>Cells</td><td>float32</td><td>1</td><td>3.750e-01</td><td>3.750e-01</td></tr>
<tr><td>nuTilda</td><td>Cells</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>nut</td><td>Cells</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>omega</td><td>Cells</td><td>float32</td><td>1</td><td>4.401e+02</td><td>4.401e+02</td></tr>
<tr><td>p</td><td>Cells</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>v2</td><td>Cells</td><td>float32</td><td>1</td><td>0.000e+00</td><td>0.000e+00</td></tr>
<tr><td>wallShearStress</td><td>Cells</td><td>float32</td><td>3</td><td>0.000e+00</td><td>0.000e+00</td></tr>
</table>

</td></tr> </table>



It looks like the cas is loaded for `time=0`, I don't know how to change the time value.

Here is for example below the U field where it's 0 everywhere expect at the inlet.


```python
mesh.plot(scalars='U')
```


    ViewInteractiveWidget(height=768, layout=Layout(height='auto', width='100%'), width=1024)


# Load the case with `vtkOpenFOAMReader`


```python
reader = vtk.vtkOpenFOAMReader()
reader.SetFileName("cyclone_test_02_without_bl.foam")
reader.UpdateInformation()
time_array = reader.GetTimeValues()
reader.SetTimeValue(time_array.GetRange()[1])  # Set read time to latest time step
reader.Update()
multi_block = pv.wrap(reader.GetOutput())
mesh = multi_block[0]

```

## Let's make a simple contour plot


```python
mesh.plot(scalars='U')
```


    ViewInteractiveWidget(height=768, layout=Layout(height='auto', width='100%'), width=1024)


## Let's use a different and more useful API

### Show the mesh 


```python
p1 = pv.Plotter()
p1.add_mesh(mesh, style="wireframe", color="w")
p1.view_xy()
p1.add_axes()
p1.show()
```


    ViewInteractiveWidget(height=768, layout=Layout(height='auto', width='100%'), width=1024)


### An example with a field contour


```python
p2 = pv.Plotter()
p2.add_mesh(mesh,scalars='U')
p2.view_xy()
p2.add_axes()
p2.show()
```


    ViewInteractiveWidget(height=768, layout=Layout(height='auto', width='100%'), width=1024)


 

### Another one with the mesh surimposed


```python
p3 = pv.Plotter()
p3.add_mesh(mesh,scalars='U', show_edges=True)
p3.view_xy()
p3.add_axes()
p3.show()
```


    ViewInteractiveWidget(height=768, layout=Layout(height='auto', width='100%'), width=1024)


### Plot over line example


```python
# Create the 2 points defining the line
a = [0, 0, 0]
b = [0, mesh.bounds[3], 0]

# Show the line in red in the domain
line = pv.Line(a, b)

p4 = pv.Plotter()
p4.view_xy()
p4.add_mesh(mesh, style="wireframe", color='white')
p4.add_mesh(line, color="r", line_width=10)
p4.add_axes()

p4.show()
```


    ViewInteractiveWidget(height=768, layout=Layout(height='auto', width='100%'), width=1024)


Let's make a plot overline ...


```python
mesh.plot_over_line(a, b, resolution=100, scalars='U')
```


    Canvas(toolbar=Toolbar(toolitems=[('Home', 'Reset original view', 'home', 'home'), ('Back', 'Back to previous …


### How to access the vector components


```python
mesh.cell_arrays
```




    pyvista DataSetAttributes
    Association: CELL
    Contains keys:
    	U
    	epsilon
    	k
    	nut
    	p




```python
U = mesh.cell_arrays[0]
np.shape(U)
```




    (12225, 3)




```python
ux = U[:,0]
uy = U[:,1]
uz = U[:,2]

# One can also use
ux = mesh['U'][:,0]
uy = mesh['U'][:,1]
uz = mesh['U'][:,2]
```


```python
mesh.cell_arrays["ux"] = ux
mesh.plot_over_line(a, b, resolution=50, scalars='ux')
```


    Canvas(toolbar=Toolbar(toolitems=[('Home', 'Reset original view', 'home', 'home'), ('Back', 'Back to previous …

