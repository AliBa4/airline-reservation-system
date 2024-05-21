# airline-reservation-system
Private Sub Button4_Click_1(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button4.Click
     If ComboBox1.Text = "USER" Then
         Dim mno As String
         mno = TextBox10.Text
         Dim ono As String
         ono = TextBox11.Text
         Dim cnPodaci As New SqlConnection
         cnPodaci.ConnectionString = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
         cnPodaci.Open()
         Dim cm As New SqlCommand
         cm.CommandText = "SELECT * FROM users where UserName = '" & mno & "' And Password = '" & ono & "'"
         cm.Connection = cnPodaci
         Dim dr As SqlDataReader
         dr = cm.ExecuteReader
 
         If dr.HasRows Then
 
 
             MsgBox(" succsessfully logged ")
             TextBox10.Text = ""
             TextBox11.Text = ""
             Me.Hide()
             Home.Show()
             dr.Close()
         Else
             Beep()
             MessageBox.Show("Your username Or password is not match", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
             TextBox10.Text = ""
             TextBox11.Text = ""
             TextBox10.Focus()
         End If
         cnPodaci.Close()
     ElseIf ComboBox1.Text = "ADMIN" Then
         If TextBox10.Text = "sagar " Or TextBox11.Text = "sagar" Then
             Beep()
             Beep()
             MsgBox("You are successfully logged.")
             TextBox10.Text = ""
             TextBox11.Text = ""
             ADMIN_Page.Show()
             Me.Hide()
         Else
             Beep()
             MessageBox.Show("Your username Or password is not match", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
             TextBox10.Text = ""
             TextBox11.Text = ""
             TextBox10.Focus()
         End If
     Else
         MessageBox.Show("Select your choice", "ADMIN or USER", MessageBoxButtons.OK, MessageBoxIcon.Information)
     End If
End Sub
Private Sub Button5_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button5.Click
     TextBox10.Text = ""
     TextBox11.Text = ""
End Sub
Private Sub Button1_Click_1(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button1.Click
     Me.Hide()
     signup.Show()
End Sub


Search Flight Code

Public Class Search_Flight
    Dim con As New SqlConnection
    Dim dt As New DataTable
    Dim adp As SqlDataAdapter
    Private Const ConnectionString As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
    Private ReadOnly Property Connection() As SqlConnection
        Get
            Dim ConnectionToFetch As New SqlConnection(ConnectionString)
            ConnectionToFetch.Open()
            Return ConnectionToFetch
        End Get
    End Property
    Public Function GetData() As DataView
        Dim SelectQry = "SELECT AirlineName,FlightNo,DepartureTime,ArrivalTime,WeekDays FROM aircraft,flights,sector where aircraft.AircraftTypeId=flights.AircraftTypeId and Flights.sectorid=sector.sectorid and Source = '" & Source.Text & "' and Destination = '" & Destination.Text & "'"
        Dim SampleSource As New DataSet
        Dim TableView As DataView
        Try
            Dim SampleCommand As New SqlCommand()
            Dim SampleDataAdapter = New SqlDataAdapter()
            SampleCommand.CommandText = SelectQry
            SampleCommand.Connection = Connection
            SampleDataAdapter.SelectCommand = SampleCommand
            SampleDataAdapter.Fill(SampleSource)
            TableView = SampleSource.Tables(0).DefaultView
        Catch ex As Exception
            Throw ex
        End Try
        Return TableView
    End Function
    Sub populatesource()
        Dim conn As New SqlConnection("Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True")
        conn.Open()
        Dim sql As New SqlCommand("Select distinct Source from Sector", conn)
        sql.CommandType = CommandType.Text
        Dim adapt As New SqlDataAdapter
        adapt.SelectCommand = sql
        adapt.SelectCommand.ExecuteNonQuery()
        Dim dset As New DataSet
        adapt.Fill(dset, "Sector")
        conn.Close()
        Source.DataSource = dset.Tables("sector")
        Source.DisplayMember = "source"
        Source.ValueMember = "source"
    End Sub
    Sub populatedestination()
        Dim conn As New SqlConnection("Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True")
        conn.Open()
        Dim sql As New SqlCommand("Select distinct destination from sector", conn)
        sql.CommandType = CommandType.Text
        Dim adapt As New SqlDataAdapter
        adapt.SelectCommand = sql
        adapt.SelectCommand.ExecuteNonQuery()
        Dim dset As New DataSet
        adapt.Fill(dset, "sector")
        conn.Close()
        Destination.DataSource = dset.Tables("sector")
        Destination.DisplayMember = "destination"
        Destination.ValueMember = "destination"
    End Sub
    Private Sub Search_Flight_Load(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles MyBase.Load
        DataGridView1.Visible = False
        populatesource()
        populatedestination()
        Source.Text = "select"
        Destination.Text = "select"
    End Sub
    Private Sub Button1_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button1.Click
        DataGridView1.Visible = True
        DataGridView1.DataSource = GetData()
    End Sub
    Private Sub Button1_MouseHover(ByVal sender As Object, ByVal e As System.EventArgs) Handles Button1.MouseHover
        ToolTip1.IsBalloon = True
        ToolTip1.UseAnimation = True
        ToolTip1.ToolTipTitle = ""
        ToolTip1.SetToolTip(Button1, "serach flights between entered two locations")
    End Sub
    Private Sub Button2_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button2.Click
        Me.Close()
        Home.Show()
    End Sub
    Private Sub Button2_MouseHover(ByVal sender As Object, ByVal e As System.EventArgs) Handles Button1.MouseHover
        ToolTip1.IsBalloon = True
        ToolTip1.UseAnimation = True
        ToolTip1.ToolTipTitle = ""
        ToolTip1.SetToolTip(Button1, "fOR EXIT")
    End Sub
    Private Sub ToolTip1_Popup(ByVal sender As System.Object, ByVal e As System.Windows.Forms.PopupEventArgs) Handles ToolTip1.Popup
    End Sub
Reservation Page

Public Class Reservation
    Dim frmPrintTicket As New Print_ticket
    Private Sub Button4_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Button4.Click
        Dim rdr As SqlDataReader = Nothing
        Dim con As SqlConnection = Nothing
        Dim cmd As SqlCommand = Nothing
        If Len(Trim(FlightNo.Text)) = 0 Then
            MessageBox.Show("Please Specify the flightNo", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(AirwayClass.Text)) = 0 Then
            MessageBox.Show("Please Specify the Class Preference of the passenger", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If

        Dim cs As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
        con = New SqlConnection(cs)
        con.Open()
        If (AirwayClass.Text) = "First Class" Then
            Dim ct As String = "SELECT  Sector.Source, Sector.Destination, Sector.FirstClassFare  from sector,flights where Flights.SectorID = Sector.SectorID and FlightNo=@FIND"
            cmd = New SqlCommand(ct)
            cmd.Connection = con
            cmd.Parameters.Add(
       New SqlParameter("@find", System.Data.SqlDbType.NChar, 10, "FlightNo"))
            cmd.Parameters("@find").Value = FlightNo.Text
            rdr = cmd.ExecuteReader()
            If rdr.Read Then
                Source.Text = rdr.GetString(0)
                Destination.Text = rdr.GetString(1)
                Fare.Text = rdr.GetString(2)
            End If
            If Not rdr Is Nothing Then
                rdr.Close()
            End If
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
        End If
        If (AirwayClass.Text) = "Business Class" Then
            Dim ct As String = "SELECT  Sector.Source, Sector.Destination, Sector.BusinessClassFare FROM  sector INNER JOIN flights ON Flights.SectorID = Sector.SectorID and FlightNo=@FIND"
            cmd = New SqlCommand(ct)
            cmd.Connection = con
            cmd.Parameters.Add(
       New SqlParameter("@find", System.Data.SqlDbType.NChar, 10, "FlightNo"))
            cmd.Parameters("@find").Value = FlightNo.Text
            rdr = cmd.ExecuteReader()
            If rdr.Read Then
                Source.Text = rdr.GetString(0)
                Destination.Text = rdr.GetString(1)
                Fare.Text = rdr.GetString(2)
            End If
            If Not rdr Is Nothing Then
                rdr.Close()
            End If
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
        End If
        If (AirwayClass.Text) = "Economy Class" Then
 
            Dim ct As String = "SELECT  Sector.Source, Sector.Destination, Sector.EconomyClassFare FROM  sector INNER JOIN flights ON Flights.SectorID = Sector.SectorID and FlightNo=@FIND"
            cmd = New SqlCommand(ct)
            cmd.Connection = con
            cmd.Parameters.Add(
       New SqlParameter("@find", System.Data.SqlDbType.NChar, 10, "FlightNo"))
            cmd.Parameters("@find").Value = FlightNo.Text
            rdr = cmd.ExecuteReader()
            If rdr.Read Then
                Source.Text = rdr.GetString(0)
                Destination.Text = rdr.GetString(1)
                Fare.Text = rdr.GetString(2)
            End If
            If Not rdr Is Nothing Then
                rdr.Close()
            End If
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
        End If
    End Sub



Cancel Reservation Page Code
Public Class Cancle_Reservation
    Dim frmRefundTicket As New print_cancle_ticket
    Dim rdr As SqlDataReader = Nothing
    Dim con As SqlConnection = Nothing
    Dim cmd As SqlCommand = Nothing
    Dim RowsAffected As Integer = 0
    Private Sub Label12_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Label12.Click
    End Sub
    Private Sub Cancel_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Cancel.Click
        If Len(Trim(PnrNo.Text)) = 0 Then
            MessageBox.Show("Please enter the PNR Number", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Trim(RStatus.Text) = "Cancelled" Then
            MessageBox.Show("This ticket is already cancelled", "Sorry", MessageBoxButtons.OK, MessageBoxIcon.Error)
            clear()
        Else
            Dim cop As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
 
            con = New SqlConnection(cop)
            con.Open()
            If Trim(AirwayClass.Text) = "First Class" And Trim(RStatus.Text) = "Confirmed" Then
                Dim cpp As String = "update scheduleFlights set FirstClassSeatAvailable=FirstClassSeatAvailable + 1 where scheduleflights.FlightNo = '" & FlightNo.Text & "' and FlightDate = '" & TravelDate.Text & "'"
                cmd = New SqlCommand(cpp)
                cmd.Connection = con
                cmd.ExecuteNonQuery()
                If Not rdr Is Nothing Then
                    rdr.Close()
                End If
                If con.State = ConnectionState.Open Then
                    con.Close()
                End If
                con.Close()
            End If
            If Trim(AirwayClass.Text) = "Business Class" And Trim(RStatus.Text) = "Confirmed" Then
                Dim cm As String = "update scheduleFlights set BusinessClassSeatAvailable=BusinessClassSeatAvailable + 1 where scheduleflights.FlightNo = '" & FlightNo.Text & "' and FlightDate = '" & TravelDate.Text & "'"
 
 
 
                cmd = New SqlCommand(cm)
                cmd.Connection = con
                cmd.ExecuteNonQuery()
                If con.State = ConnectionState.Open Then
                    con.Close()
                End If
                con.Close()
            End If
            If Trim(AirwayClass.Text) = "Economy Class" And Trim(RStatus.Text) = "Confirmed" Then
                Dim cl As String = "update scheduleFlights set EconomyClassSeatAvailable=EconomyClassSeatAvailable + 1 where scheduleflights.FlightNo = '" & FlightNo.Text & "' and FlightDate = '" & TravelDate.Text & "'" 
                cmd = New SqlCommand(cl)
                cmd.Connection = con
                cmd.ExecuteNonQuery()
                If con.State = ConnectionState.Open Then
                    con.Close()
                End If
                con.Close()
            End If
 
            Dim cs As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
            con = New SqlConnection(cs)
            con.Open()
            Dim ct As String = "insert into CancelReservation(PnrNo,FirstName,LastName,RefundAmount,date) values(@INSERT1,@INSERT2,@INSERT3,@INSERT4,@INSERT5)"
            cmd =
            New SqlCommand(ct)
            cmd.Connection = con
            cmd.Parameters.Add(
            New SqlParameter("@INSERT1", System.Data.SqlDbType.NChar, 10, "PnrNo"))
            cmd.Parameters.Add(
            New SqlParameter("@INSERT2", System.Data.SqlDbType.NChar, 20, "Firstname"))
            cmd.Parameters.Add(
            New SqlParameter("@INSERT3", System.Data.SqlDbType.NChar, 20, "LastName"))
            cmd.Parameters.Add(
            New SqlParameter("@INSERT4", System.Data.SqlDbType.NChar, 10, "RefundAmount"))
            cmd.Parameters.Add(
           New SqlParameter("@INSERT5", System.Data.SqlDbType.NChar, 30, "date"))
            cmd.Parameters(
           "@INSERT1").Value = PnrNo.Text
            cmd.Parameters(
           "@INSERT2").Value = Fname.Text
            cmd.Parameters(
           "@INSERT3").Value = Lname.Text
            cmd.Parameters(
           "@INSERT4").Value = AmtRefund.Text
            cmd.Parameters(
           "@INSERT5").Value = DateTime.Now
            cmd.ExecuteReader()
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
            con.Close()
            Dim cd As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
            con = New SqlConnection(cd)
            con.Open()
            Dim coz As String = "insert into Refunds(PnrNo,Date,RefundAmount) VALUES (@INSERT1,@INSERT2,@INSERT3)"
            cmd =
            New SqlCommand(coz)
            cmd.Connection = con
            cmd.Parameters.Add(
            New SqlParameter("@INSERT1", System.Data.SqlDbType.NChar, 10, "PnrNo"))
            cmd.Parameters.Add(
            New SqlParameter("@INSERT2", System.Data.SqlDbType.NChar, 30, "date"))
 
            cmd.Parameters.Add(
            New SqlParameter("@INSERT3", System.Data.SqlDbType.NChar, 10, "RefundAmount"))
            cmd.Parameters("@INSERT1").Value = PnrNo.Text
            cmd.Parameters("@INSERT2").Value = Today
            cmd.Parameters("@INSERT3").Value = AmtRefund.Text
            cmd.ExecuteReader()
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
            con.Close()
            Dim ck As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
            con = New SqlConnection(ck)
            con.Open()
            Dim co As String = "Update Reservations set RStatus = 'Cancelled' where PnrNo ='" & PnrNo.Text & "'"
            cmd =
            New SqlCommand(co)
            cmd.Connection = con
            cmd.Parameters.Add(
            New SqlParameter("Cancelled", System.Data.SqlDbType.NChar, 10, "RStatus"))
            cmd.Parameters(
            "cancelled").Value = RStatus.Text
            cmd.ExecuteReader()
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
            con.Close()
            MessageBox.Show("Cancelled", "Airline reservation", MessageBoxButtons.OK, MessageBoxIcon.Information)
            FlightNo.Text = ""
            Fname.Text = ""
            Lname.Text = ""
            Age.Text = ""
            Gender.Text = ""
            TravelDate.Text = ""
            AirwayClass.Text = ""
            RStatus.Text = ""
            Fare.Text = ""
            AmtRefund.Text = ""
            Source.Text = ""
            Destination.Text = ""
        End If
    End Sub


Aircraft Page Code
Public Class Aircraft
    Dim con As New SqlConnection
    Dim dt As New DataTable
    Dim adp As SqlDataAdapter
    Private Const ConnectionString As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
    Private ReadOnly Property Connection() As SqlConnection
        Get
            Dim ConnectionToFetch As New SqlConnection(ConnectionString)
            ConnectionToFetch.Open()
            Return ConnectionToFetch
        End Get
    End Property
    Public Function GetData() As DataView
        Dim SelectQry = "SELECT * FROM AIRCRAFT "
        Dim SampleSource As New DataSet
        Dim TableView As DataView
        Try
            Dim SampleCommand As New SqlCommand()
            Dim SampleDataAdapter = New SqlDataAdapter()
            SampleCommand.CommandText = SelectQry
            SampleCommand.Connection = Connection
            SampleDataAdapter.SelectCommand = SampleCommand
            SampleDataAdapter.Fill(SampleSource)
            TableView = SampleSource.Tables(0).DefaultView
        Catch ex As Exception
            Throw ex
        End Try
        Return TableView
    End Function
    Private Sub Submit_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Submit.Click
        Dim rdr As SqlDataReader = Nothing
        Dim con As SqlConnection = Nothing
        Dim cmd As SqlCommand = Nothing
        Dim cs As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
        con = New SqlConnection(cs)
        con.Open()
        Dim ct As String = "select * from aircraft where AircraftTypeID=@find"
        cmd = New SqlCommand(ct)
        cmd.Connection = con
        cmd.Parameters.Add(
       New SqlParameter("@find", System.Data.SqlDbType.NChar, 10, "AirCraftTypeId"))
        cmd.Parameters("@find").Value = AircraftTypeId.Text
        rdr = cmd.ExecuteReader()
        If Not rdr.Read Then
            MsgBox("Sorry ! No Records Found")
            AircraftTypeId.Text = ""
            AircraftTypeId.Focus()
        Else
            AircraftTypeId.Text = rdr.GetString(0)
            AirlineName.Text = rdr.GetString(1)
            FirstClassSeats.Text = rdr.GetString(2)
            BusinessClassSeats.Text = rdr.GetString(3)
            EconomyClassSeats.Text = rdr.GetString(4)
        End If
        If Not rdr Is Nothing Then
            rdr.Close()
        End If
        If con.State = ConnectionState.Open Then
            con.Close()
        End If
    End Sub
    Private Sub FirstClassSeats_KeyPress(ByVal sender As Object, ByVal e As System.Windows.Forms.KeyPressEventArgs) Handles FirstClassSeats.KeyPress
        If (e.KeyChar < Chr(48) Or e.KeyChar > Chr(57)) And e.KeyChar <> Chr(8) Then
            e.Handled = True
        End If
    End Sub
 Private Sub EconomyClassSeats_KeyPress(ByVal sender As Object, ByVal e As System.Windows.Forms.KeyPressEventArgs) Handles EconomyClassSeats.KeyPress
        If (e.KeyChar < Chr(48) Or e.KeyChar > Chr(57)) And e.KeyChar <> Chr(8) Then
            e.Handled = True
        End If
    End Sub
    Private Sub BusinessClassSeats_KeyPress(ByVal sender As Object, ByVal e As System.Windows.Forms.KeyPressEventArgs) Handles BusinessClassSeats.KeyPress
        If (e.KeyChar < Chr(48) Or e.KeyChar > Chr(57)) And e.KeyChar <> Chr(8) Then
            e.Handled = True
        End If
    End Sub
    Private Sub Cancel_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Cancel.Click
        AircraftTypeId.Text = ""
    End Sub
    Sub populateAirline()
        Dim cs As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
        con = New SqlConnection(cs)
        con.Open()
        adp = New SqlDataAdapter("select distinct airlinename from aircraft ", con)
        adp.Fill(dt)
        AirlineName1.DataSource = dt
        AirlineName1.DisplayMember = "AirlineName"
        AirlineName1.SelectedIndex = -1
        AirlineName1.Text = "select"
Sector Page Code
Public Class Sector
    Private Sub Submit_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Submit.Click
        Dim rdr As SqlDataReader = Nothing
        Dim con As SqlConnection = Nothing
        Dim cmd As SqlCommand = Nothing
        Dim cs As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
        con = New SqlConnection(cs)
        con.Open()
        Dim ct As String = "select * from sector where sectorID=@find"
        cmd = New SqlCommand(ct)
        cmd.Connection = con
        cmd.Parameters.Add(
       New SqlParameter("@find", System.Data.SqlDbType.NChar, 10, "SectorId"))
        cmd.Parameters("@find").Value = SectorID.Text
        rdr = cmd.ExecuteReader()
        If Not rdr.Read Then
            MsgBox("Sorry ! No Records Found")
            SectorID.Text = ""
            SectorID.Focus()
        Else
            SectorID.Text = rdr.GetString(0)
            Source.Text = rdr.GetString(1)
            Destination.Text = rdr.GetString(2)
            WeekDays.Text = rdr.GetString(3)
            FirstClassFare.Text = rdr.GetString(4)
            BusinessClassFare.Text = rdr.GetString(5)
            EconomyClassFare.Text = rdr.GetString(6)
        End If
        If Not rdr Is Nothing Then
            rdr.Close()
        End If
        If con.State = ConnectionState.Open Then
            con.Close()
        End If
    End Sub
    Private Sub Cancel_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Cancel.Click
        Me.SectorID.Enabled = True
        Me.SectorID.Text = ""
        Me.Source.Text = ""
        Me.Destination.Text = ""
        Me.WeekDays.Text = ""
        Me.FirstClassFare.Text = ""
        Me.BusinessClassFare.Text = ""
        Me.EconomyClassFare.Text = ""
        Me.SectorID.Focus()
    End Sub
    Private Sub NewRecord_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles NewRecord.Click
        Me.SectorID.Text = ""
        Me.Source.Text = ""
        Me.Destination.Text = ""
        Me.WeekDays.Text = ""
        Me.FirstClassFare.Text = ""
        Me.BusinessClassFare.Text = ""
        Me.EconomyClassFare.Text = ""
    End Sub
    Private Sub Add_Click(ByVal sender As System.Object, ByVal e As System.EventArgs) Handles Add.Click
        If Len(Trim(SectorID.Text)) = 0 Then
            MessageBox.Show("Please enter the Sector ID", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(Source.Text)) = 0 Then
            MessageBox.Show("Please enter the Source Location", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(Destination.Text)) = 0 Then
            MessageBox.Show("Please enter the Source Location", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(Source.Text)) = 0 Then
            MessageBox.Show("Please enter the Destination Location", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(WeekDays.Text)) = 0 Then
            MessageBox.Show("Please select the 1st week day", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(WeekDays.Text)) = 0 Then
            MessageBox.Show("Please select the week days", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(FirstClassFare.Text)) = 0 Then
            MessageBox.Show("Please enter the 1st class fare", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(BusinessClassFare.Text)) = 0 Then
            MessageBox.Show("Please enter the business class fare", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        If Len(Trim(EconomyClassFare.Text)) = 0 Then
            MessageBox.Show("Please enter the economy class fare", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            Exit Sub
        End If
        Dim rdr As SqlDataReader = Nothing
        Dim con As SqlConnection = Nothing
        Dim cmd As SqlCommand = Nothing
        Dim cs As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
        con = New SqlConnection(cs)
        con.Open()
        Dim ct As String = "select SectorID from sector where SectorID=@find"
        cmd = New SqlCommand(ct)
        cmd.Connection = con
        cmd.Parameters.Add(
       New SqlParameter("@find", System.Data.SqlDbType.NChar, 10, "SectorId"))
        cmd.Parameters("@find").Value = SectorID.Text
        rdr = cmd.ExecuteReader()
        If rdr.Read Then
            MessageBox.Show("Sector ID Already Exists", "Input Error", MessageBoxButtons.OK, MessageBoxIcon.Error)
            SectorID.Text = ""
            Source.Text = ""
            Destination.Text = ""
            WeekDays.Text = ""
            FirstClassFare.Text = ""
            BusinessClassFare.Text = ""
            EconomyClassFare.Text = ""
            If Not rdr Is Nothing Then
                rdr.Close()
            End If
        Else 
            Dim ck As String = "Data Source=.\SQLEXPRESS;AttachDbFilename=D:\airline\airline\AirlineReservationSystem.mdf;Integrated Security=True;Connect Timeout=30;User Instance=True"
            con = New SqlConnection(ck)
            con.Open()
            Dim cm As String = "insert into sector(SectorID,Source,Destination,WeekDays,FirstClassFare,BusinessClassFare,EconomyClassFare) VALUES (@INSERT1,@INSERT2,@INSERT3,@INSERT4,@INSERT5,@INSERT6,@INSERT7)"
            cmd =
            New SqlCommand(cm)
            cmd.Connection = con
            cmd.Parameters.Add(New SqlParameter("@INSERT1", System.Data.SqlDbType.NChar, 10, "SectorID"))
            cmd.Parameters.Add(New SqlParameter("@INSERT2", System.Data.SqlDbType.NChar, 20, "Source"))
            cmd.Parameters.Add(New SqlParameter("@INSERT3", System.Data.SqlDbType.NChar, 20, "Destination"))
            cmd.Parameters.Add(New SqlParameter("@INSERT4", System.Data.SqlDbType.VarChar, 80, "WeekDays"))
            cmd.Parameters.Add(New SqlParameter("@INSERT5", System.Data.SqlDbType.NChar, 10, "FirstClassFare"))
            cmd.Parameters.Add(New SqlParameter("@INSERT6", System.Data.SqlDbType.NChar, 10, "BusinessClassFare"))
            cmd.Parameters.Add(New SqlParameter("@INSERT7", System.Data.SqlDbType.NChar, 10, "EconomyClassFare"))
            cmd.Parameters("@INSERT1").Value = SectorID.Text
            cmd.Parameters("@INSERT2").Value = Source.Text
            cmd.Parameters("@INSERT3").Value = Destination.Text
            cmd.Parameters("@INSERT4").Value = WeekDays.Text
            cmd.Parameters("@INSERT5").Value = FirstClassFare.Text
            cmd.Parameters("@INSERT6").Value = BusinessClassFare.Text
            cmd.Parameters("@INSERT7").Value = EconomyClassFare.Text
            cmd.ExecuteReader()
            If con.State = ConnectionState.Open Then
                con.Close()
            End If
            con.Close()
            MsgBox("Successfully Added")
            Me.SectorID.Text = ""
            Me.Source.Text = ""
            Me.Destination.Text = ""
            Me.WeekDays.Text = ""
            Me.FirstClassFare.Text = ""
