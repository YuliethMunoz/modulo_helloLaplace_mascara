from __main__ import vtk, qt, ctk, slicer



class MASCARAS:
  def __init__(self, parent):
    parent.title = "Comparar metabolismo"
    parent.categories = ["Mascaras"]
    parent.dependencies = []
    parent.contributors = ["Laura Carolina Rozo Hoyos",
                           "Yulieth Katerine Muñoz Zapata)",
                           "Estudiantes de bioingeniería, Universidad de Antioquia"] # replace with "Firstname Lastname (Org)"
    parent.helpText = """
    Example of scripted loadable extension for the HelloLaplace tutorial.
    """
    parent.acknowledgementText = """
    This file was originally developed by Jean-Christophe Fillion-Robin, Kitware Inc.,
Steve Pieper, Isomics, Inc., and Sonia Pujol, Brigham and Women's Hospital and was 
partially funded by NIH grant 3P41RR013218-12S1 (NAC) and is part of the National Alliance 
for Medical Image Computing (NA-MIC), funded by the National Institutes of Health through the 
NIH Roadmap for Medical Research, Grant U54 EB005149.""" # replace with organization, grant and thanks.
    self.parent = parent

#
# qHelloPythonWidget
#

class HelloLaplaceWidget:
  def __init__(self, parent = None):
    if not parent:
      self.parent = slicer.qMRMLWidget()
      self.parent.setLayout(qt.QVBoxLayout())
      self.parent.setMRMLScene(slicer.mrmlScene)
    else:
      self.parent = parent
    self.layout = self.parent.layout()
    if not parent:
      self.setup()
      self.parent.show()

  def setup(self):
    # Collapsible button
    self.laplaceCollapsibleButton = ctk.ctkCollapsibleButton()
    self.laplaceCollapsibleButton.text = "Primera mascara"
    self.layout.addWidget(self.laplaceCollapsibleButton)

    # Layout within the laplace collapsible button
    self.laplaceFormLayout = qt.QFormLayout(self.laplaceCollapsibleButton)

    # the volume selectors
##    self.inputFrame = qt.QFrame(self.laplaceCollapsibleButton)
##    self.inputFrame.setLayout(qt.QHBoxLayout())
##    self.laplaceFormLayout.addWidget(self.inputFrame)
##    self.inputSelector = qt.QLabel("Input Volume: ", self.inputFrame)
##    self.inputFrame.layout().addWidget(self.inputSelector)
##    self.inputSelector = slicer.qMRMLNodeComboBox(self.inputFrame)
##    self.inputSelector.nodeTypes = ( ("vtkMRMLScalarVolumeNode"), "" )
##    self.inputSelector.addEnabled = False
##    self.inputSelector.removeEnabled = False
##    self.inputSelector.setMRMLScene( slicer.mrmlScene )
##    self.inputFrame.layout().addWidget(self.inputSelector)
##    self.parent.connect('mrmlSceneChanged(vtkMRMLScene*)', self.inputSelector, 'setMRMLScene(vtkMRMLScene*)')

      #selector de imgen a centrar
    self.inputSelector = slicer.qMRMLNodeComboBox()
    self.inputSelector.objectName = 'imagenSelector'
    self.inputSelector.toolTip = 'Seleccione la imagen que desea centrar'
    self.inputSelector.nodeTypes = ['vtkMRMLScalarVolumeNode']
    self.inputSelector.noneEnabled = True
    self.inputSelector.addEnabled = False  # Se quita la posibilidad al usuario de crear un nuevo nodo con este widget
    self.inputSelector.removeEnabled = False  # Se le quita al usuario la posibilidad de eliminar el nodo seleccionado en ese momento
    #self.inputSelector.connect('currentNodeChanged(bool)')
    self.inputSelector.setMRMLScene(slicer.mrmlScene)
    self.laplaceFormLayout.addRow("Volumen a centrar:", self.inputSelector)
    self.parent.connect('mrmlSceneChanged(vtkMRMLScene*)', self.inputSelector, 'setMRMLScene(vtkMRMLScene*)')

            #selector de imgen a centrar
    self.outputSelector = slicer.qMRMLNodeComboBox()
    self.outputSelector.objectName = 'imagenSelector'
    self.outputSelector.toolTip = 'Seleccione la imagen que desea centrar'
    self.outputSelector.nodeTypes = ['vtkMRMLScalarVolumeNode']
    self.outputSelector.noneEnabled = True
    self.outputSelector.addEnabled = True  # Se quita la posibilidad al usuario de crear un nuevo nodo con este widget
    self.outputSelector.removeEnabled = False  # Se le quita al usuario la posibilidad de eliminar el nodo seleccionado en ese momento
    #self.outputSelector.connect('currentNodeChanged(bool)')
    self.outputSelector.setMRMLScene(slicer.mrmlScene)
    self.laplaceFormLayout.addRow("Volumen a centrar:", self.outputSelector)
    self.parent.connect('mrmlSceneChanged(vtkMRMLScene*)', self.outputSelector, 'setMRMLScene(vtkMRMLScene*)')  

    # Apply button
    laplaceButton = qt.QPushButton("Guardar Volumes")
    laplaceButton.toolTip = "Run the Laplace Operator."
    self.laplaceFormLayout.addWidget(laplaceButton)
    laplaceButton.connect('clicked(bool)', self.Guardar)
    # Add vertical spacer
    self.layout.addStretch(1)



    # Set local var as instance attribute
    self.laplaceButton = laplaceButton

  def Guardar(self):
      inputVolume = self.inputSelector.currentNode()
      outputVolume = self.outputSelector.currentNode()
##    slicer.mrmlScene.AddNode(outputVolume)
##    outputVolume.SetName('salida')
    

   #   def TH(inputVolume,outputVolume)
      #slicer.cli.run(slicer.modules.thresholdscalarvolume,None,parameters)  
      parameters={}
      parameters['InputVolume']= inputVolume
      parameters['OutputVolume']= outputVolume
      parameters['ThresholdType']= 'Above'
      parameters['Lower']= 42
      parameters['Upper']= 42
      parameters['Outsite']=0
      parameters['ThresholdValue']= 42
      print(parameters)
      cliNode=slicer.cli.run(slicer.modules.thresholdscalarvolume,None,parameters, wait_for_completion=True)
      print(cliNode)
      parameters={}
      parameters['InputVolume']= inputVolume
      parameters['OutputVolume']= outputVolume
      parameters['ThresholdType']= 'Below'
      parameters['Lower']= 42
      parameters['Upper']= 42
      parameters['Outsite']=0
      parameters['ThresholdValue']= 42
      print(parameters)
      cliNode=slicer.cli.run(slicer.modules.thresholdscalarvolume,None,parameters, wait_for_completion=True)
      print(cliNode)
       # return (slicer.cli.run(slicer.modules.thresholdscalarvolume,None,parameters))
      #TH(inputVolume,outputVolume)
      #return 
    


      
    
  
    
    # run the filter
    # Insert code here
    # (be sure to match indentation)
   # ijkToRAS = vtk.vtkMatrix4x4()
    #inputVolume.GetIJKToRASMatrix(ijkToRAS)
    #outputVolume.SetIJKToRASMatrix(ijkToRAS)
    #outputVolume.SetAndObserveImageData(laplacian.GetOutput())
    # make the output volume appear in all the slice views
    #selectionNode = slicer.app.applicationLogic().GetSelectionNode()
    #selectionNode.SetReferenceActiveVolumeID(outputVolume.GetID())
    #slicer.app.applicationLogic().PropagateVolumeSelection(0)

