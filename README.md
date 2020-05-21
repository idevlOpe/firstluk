--Drop database if exists PackXprezDB
--Create database PackXprezDB
--USe PackXprezDB

drop table if exists DAddress
drop table if exists AWB
drop table if exists Shipment
drop table if exists ServiceArea
drop table if exists AddressTable
drop table if exists Customer

create table Customer
(
	[Name] varchar(50) constraint ck_Name_Customer check([Name] not like '%[^A-Z ]%') not null,
	[EmailId] varchar(20)  constraint pk_EmailId_Customer primary Key CONSTRAINT chk_EmailId CHECK([EmailId] LIKE '%@%.com'),
	[Password] varchar(16) constraint ck_Password check (len([Password]) >= 8) not null,
	[Contact] numeric(10) constraint ck_Contact check([Contact]>999999999) unique not null,
)

create table Address
(
	[AddressId] int CONSTRAINT pk_AddressId_AddressTable PRIMARY KEY IDENTITY(1,1),	
	[EmailId] varchar(20) constraint fk_AddressEmailId_AddressTable Foreign key references Customer(EmailId) not null,
	[BuildingNo] varchar(10) check([BuildingNo] not like '%[^A-Z0-9 ]%') not null,
	[StreetNo] varchar(10) check([StreetNo] not like '%[^A-Z0-9 ]%') not null,
	[Locality] varchar(50) check([Locality] not like '%[^A-Z ]%') not null,
	[Pincode] numeric(6) constraint chk_Pincode_AddressTable Check([Pincode]>99999) not null
	
)

create table ServiceArea
(
	[Pincode] numeric(6) constraint chk_Pincode_ServiceArea Check([Pincode]>99999) primary key   
)

create table Shipment
(
	[EmailId] varchar(20) constraint fk_shipmentEmailId_Shipment foreign Key references Customer(EmailId) not null,
	[PUCode] numeric(6) constraint fk_PUCode_Shipment foreign key references ServiceArea(Pincode) not null, 
	[DCode] numeric(6) constraint fk_DCode_Shipment foreign key references ServiceArea(Pincode) not null,
	[Type] varchar(10) constraint ck_Type_Shipment check([Type] in ('Heavy','Perishable','Fragile')) not null,
	[Weight] numeric(6,3) check([Weight]>0) not null,
	[Length] int check([Length]>0) not null,
	[Breadth] int check([Breadth]>0) not null,
	[Height] int check([Height]>0) not null,
	[DeliveryOpt] varchar(10) constraint ck_DeliveryOption_Shipment check([DeliveryOpt] in ('Standard','Express','Overnight')) not null,
	[TimeSlot] date check ([TimeSlot]>getdate()) not null,
	[AddressId] int constraint fk_AddressId_Shipment foreign key references AddressTable([AddressId]) not null,
	[Insurance] char(1) constraint ck_Insurance_Shipment check([Insurance] in ('Y','N')) not null,
	[Packaging] char(1) constraint ck_Packaging_Shipment check([Packaging] in ('Y','N')) not null,
	[Amount] numeric(6,2) not null, 
	[TransId] bigint constraint pk_TransactionId_Shipment primary key identity(1000,1),
	[Status] varchar(10) constraint ck_Status_Shipment check([Status] in ('Success','Failure'))
)

create table AWB
(
	[AWBId] bigint constraint pk_AWBId_AWB primary key identity(100000000,3),
	[TransId] bigint constraint fk_TrasactionId_AWB foreign Key references Shipment([TransId]) unique not null
)

create table DAddress
(
	[TransId] bigint constraint fk_TrasactionId_DAddress foreign Key references Shipment([TransId]) unique not null,
	[BuildingNo] varchar(10) check([BuildingNo] not like '%[^A-Z0-9 ]%') not null,
	[StreetNo] varchar(10) check([StreetNo] not like '%[^A-Z0-9 ]%') not null,
	[Locality] varchar(50) check([Locality] not like '%[^A-Z ]%') not null,
	[Pincode] numeric(6) constraint chk_Pincode_DAddress Check([Pincode]>99999) constraint fk_Pincode_DAddress foreign key references ServiceArea([Pincode]) not null,
	[Contact] numeric(10) constraint ck_Contact_DAddress check(Contact>999999999) not null
)



Insert into Customer([Name],[EmailId],[Password],[Contact]) values ('Pop','kppnesh@gmail.com','Abc1231!',9629508158)
Insert into Customer([Name],[EmailId],[Password],[Contact]) values ('Albert raja','albert@hotmail.com','12345678',8524981801)
Insert into Customer([Name],[EmailId],[Password],[Contact]) values ('Rex','rex@mail.com','12345678',1234567890)
Insert into Customer([Name],[EmailId],[Password],[Contact]) values ('Quiop','quiop@mail.com','987654321',9865323265)
go

insert into AddressTable([EmailId],[BuildingNo],[StreetNo],[Locality],[Pincode]) values ('rex@mail.com','21D','23','Ismailpuram',625009)
insert into AddressTable([EmailId],[BuildingNo],[StreetNo],[Locality],[Pincode]) values ('rex@mail.com','22D','23','Chromepet',625028)
insert into AddressTable([EmailId],[BuildingNo],[StreetNo],[Locality],[Pincode]) values ('albert@hotmail.com','2D','3','bannari',123456)
insert into AddressTable([EmailId],[BuildingNo],[StreetNo],[Locality],[Pincode]) values ('quiop@mail.com','22D','23','Dovel',620028)
insert into AddressTable([EmailId],[BuildingNo],[StreetNo],[Locality],[Pincode]) values ('kppnesh@gmail.com','3A','9','IT Park',635143)
go

insert into ServiceArea values (625009)
insert into ServiceArea values (625028)
insert into ServiceArea values (625010)
insert into ServiceArea values (635143)
go

insert into Shipment([EmailId],[PUCode],[DCode],[Type],[Weight],[Length],[Breadth],[Height],[DeliveryOpt],[TimeSlot],[AddressId],[Insurance],[Packaging],[Amount],[Status])
values ('kppnesh@gmail.com',635143,625009,'Heavy',100,10,5,10,'Standard','2020-4-15',5,'Y','N',2000,'Success')
insert into Shipment([EmailId],[PUCode],[DCode],[Type],[Weight],[Length],[Breadth],[Height],[DeliveryOpt],[TimeSlot],[AddressId],[Insurance],[Packaging],[Amount],[Status])
values ('kppnesh@gmail.com',635143,625009,'Fragile',100,10,5,10,'Overnight','2020-7-15',5,'Y','Y',500,'Failure')
insert into Shipment([EmailId],[PUCode],[DCode],[Type],[Weight],[Length],[Breadth],[Height],[DeliveryOpt],[TimeSlot],[AddressId],[Insurance],[Packaging],[Amount],[Status])
values ('rex@mail.com',625009,625028,'Perishable',15,10,15,60,'Express','2020-5-25',1,'N','Y',500,'Success')
go

insert into AWB values (1001)
insert into AWB values (1000)
go

insert into DAddress([TransId],[BuildingNo],[StreetNo],[Locality],[Pincode],[Contact]) values (1000,'1','10','Ismailpuram',625009,9600000015)
insert into DAddress([TransId],[BuildingNo],[StreetNo],[Locality],[Pincode],[Contact]) values (1001,'10','2','AnnaNagar',625009,9600000015)
insert into DAddress([TransId],[BuildingNo],[StreetNo],[Locality],[Pincode],[Contact]) values (1002,'16','5','Salipuram',625028,9600125215)
go

select * from Customer
select * from AddressTable
select * from ServiceArea
select * from Shipment
select * from AWB
select * from DAddress


-------------------------Functions--------------------------------------------------------------------------------------




drop function if exists dbo.ufn_ValidateCustomer;
drop function if exists dbo.ufn_TrackShipment;
drop function if exists dbo.ufn_GetAddress;
drop function if exists dbo.ufn_GetShipmentHistory;
drop function if exists dbo.ufn_CheckServiceAvailability;

go 

-------------Validate the customer-------------------
CREATE FUNCTION ufn_ValidateCustomer(@EmailId varchar(20), @Password varchar(16))
RETURNS bit
AS
BEGIN
     DECLARE @Validation bit
     IF NOT EXISTS(SELECT [Name] FROM Customer where [EmailId]= @EmailId AND [Password]= @Password )
          SET @Validation = 0
     ELSE
          SELECT @Validation = 1
     RETURN @Validation
END
GO

select dbo.ufn_ValidateCustomer('kppnesh@gmail.com','Abc1231!')
go

-------------Fetch the shipment details---------------------
CREATE FUNCTION ufn_TrackShipment(@AWBId BIGINT)
RETURNS varchar(10)
AS
Begin
    declare @Status varchar(10)
    set @Status = (select [Status] from Shipment where [TransId]=(select [TransId] from AWB where [AWBId]=@AWBId))
    return @Status
end
go

select dbo.ufn_TrackShipment(100000003)
go

--------------Fetch Address of a customer---------------------
CREATE FUNCTION ufn_GetAddress(@EmailId varchar(20))
RETURNS TABLE
AS
RETURN
    (select * from AddressTable where [EmailId]=@EmailId)
GO

SELECT * FROM ufn_GetAddress('rex@mail.com')
go

---------------Package history of a customer-------------------
CREATE FUNCTION ufn_GetShipmentHistory(@EmailId varchar(20))
RETURNS @ShipmentDetails TABLE([TransId] bigint,[AWSNumber] bigint,
        [FromLocation] varchar(200),[ToLocation] varchar(200),[Status] varchar(10))
AS
BEGIN
     INSERT into @ShipmentDetails
        SELECT S.[TransId],A.[AWBId],
        CONCAT('#'+Ad.[BuildingNo],',',Ad.[Locality],' ',Ad.[StreetNo],'nd Street',', ', Ad.[Pincode]),
        CONCAT('#'+D.[BuildingNo],',',D.[Locality],' ',D.[StreetNo],'nd Street',', ', D.[Pincode]),
        S.[Status]
        FROM Shipment S inner join AWB A on S.[TransId]=A.[TransId]
        inner join DAddress D on D.[TransId]=S.[TransId]
        inner join AddressTable Ad on S.[AddressId]=Ad.[AddressId]
        WHERE S.[EmailId]=@EmailId
     RETURN
END
GO

SELECT * FROM ufn_GetShipmentHistory('kppnesh@gmail.com')
go


--------------------Check Service Availability----------------------
CREATE FUNCTION ufn_CheckServiceAvailability(@PUCode numeric(6), @DCode numeric(6))
RETURNS Int
AS
BEGIN
    DECLARE @Availability int
    IF NOT EXISTS(SELECT * FROM ServiceArea where [Pincode]= @PUCode)
         set @Availability= -1
    else IF NOT EXISTS(SELECT * FROM ServiceArea where [Pincode]= @DCode)
         set @Availability= -2
    else
         SET @Availability = 1
    RETURN @Availability
END
GO

select dbo.ufn_CheckServiceAvailability(625009,625009)
go




-------------------------Stored Procedures------------------------------------------------------------------------------------


drop Procedure if exists usp_InsertCustomer
drop Procedure if exists usp_CreateShipment
drop Procedure if exists usp_InsertAddress
drop Procedure if exists usp_DeleteAddress
drop Procedure if exists usp_UpdateCustomer

go

--------------------------------------insert New Customer--------------------------------------------------
CREATE PROCEDURE usp_InsertCustomer 
(
    @Name varchar(50),
    @EmailId varchar(20),
    @Password CHAR(16),
    @BuildingNo varchar(10),
    @StreetNo varchar(10),
    @Locality varchar(50),
    @Pincode numeric(6),
    @Contact numeric(10)
) 
AS 
BEGIN
BEGIN TRY
    IF EXISTS (SELECT [EmailId] FROM Customer WHERE [EmailId]=@EmailId)
    BEGIN
       RETURN -1
    END
    Begin
        Begin tran
            Insert into Customer([Name],[EmailId],[Password],[Contact]) 
                values (@Name,@EmailId,@Password,@Contact)
            insert into AddressTable([EmailId],[BuildingNo],[StreetNo],[Locality],[Pincode])
                values (@EmailId,@BuildingNo,@StreetNo,@Locality,@Pincode)
        Commit
    end
    return 1
END TRY

BEGIN CATCH
    ROLLBACK
    RETURN -99
END CATCH 
END  
GO 

Declare @ReturnValue int
exec @ReturnValue=usp_InsertCustomer
'Siva','sivu@abc.xyz','Siva1234','6a','2','Koil',625078,9638527410
select @ReturnValue as ReturnValue
go

------------------------------------Create Shipment------------------------------------------------------------
create procedure usp_CreateShipment
(
    @EmailId varchar(20),
    @PUCode numeric(6),
    @DCode numeric(6),
    @Type varchar(10),
    @Weight numeric(6,3),
    @Length int,
    @Breadth int,
    @Height int,
    @DeliveryOpt varchar(10),
    @TimeSlot date,
    @AddressId int,
    @Insurance Char(1),
    @Packing Char(1),
    @Amount numeric(6,2),
    @TransId bigint out
)
AS 
BEGIN
BEGIN TRY
    IF not EXISTS (SELECT [Pincode] FROM ServiceArea WHERE [Pincode]=@PUCode)
        return -1
    IF not EXISTS (SELECT [Pincode] FROM ServiceArea WHERE [Pincode]=@DCode)
        return -2
    IF not EXISTS (SELECT [EmailId] FROM Customer WHERE [EmailId]=@EmailId)
        return -3
    Begin
        Begin tran
            insert into Shipment([EmailId],[PUCode],[DCode],[Type],[Weight],[Length],[Breadth],[Height],[DeliveryOpt],[TimeSlot],[AddressId],[Insurance],[Packaging],[Amount])
            values (@EmailId,@PUCode,@DCode,@Type,@Weight,@Length,@Breadth,@Height,@DeliveryOpt,@TimeSlot,@AddressId,@Insurance,@Packing,@Amount)
            set @TransId=IDENT_CURRENT('Shipment')
        Commit
    end
    return 1
END TRY

BEGIN CATCH
    ROLLBACK
    RETURN -99
END CATCH 
END  
GO 

Declare @ReturnValue int,@TransId bigint
exec @ReturnValue=usp_CreateShipment
'rex@mail.com',625009,625028,'Fragile',15,10,15,60,'Express','2020-5-25',1,'N','Y',500,@TransId out
select @ReturnValue as ReturnValue,@TransId as TransactionNumber
go

----------------------------Add Address-------------------------
CREATE PROCEDURE usp_InsertAddress 
(
    @EmailId varchar(20),
    @BuildingNo varchar(10),
    @StreetNo varchar(10),
    @Locality varchar(50),
    @Pincode numeric(6),
    @AddressId int out
) 
AS 
BEGIN
BEGIN TRY
    IF not EXISTS (SELECT [EmailId] FROM Customer WHERE [EmailId]=@EmailId)
       RETURN -1
    Begin
        Begin tran
            insert into AddressTable values(@EmailId,@BuildingNo,@StreetNo,@Locality,@Pincode)
            set @AddressId=@@IDENTITY
        Commit
    end
    return 1
END TRY

BEGIN CATCH
    ROLLBACK
    RETURN -99
END CATCH 
END  
GO 

Declare @ReturnValue int,@AddressId int
exec @ReturnValue=usp_InsertAddress
'sivu@abc.xyz','2D','3','Mahal',625009,@AddressId out    
select @ReturnValue as ReturnValue,@AddressId as AddressId
go

--------------------------Remove Address(Optional)-------------------------------
create procedure usp_DeleteAddress
(
    @AddressId int
)
as
begin
    begin Try
        Delete from AddressTable where [AddressId]=@AddressId
        return 1
    end try
    begin catch
        return -99
    end catch
end
go

declare @Return int
exec @Return=usp_DeleteAddress 7
select @Return as ReturnValue
go



----------------------------------Edit customer(Optional)-----------------------------------------------------
CREATE PROCEDURE usp_UpdateCustomer 
(
    @Name varchar(50),
    @EmailId varchar(20),
    @Password CHAR(16),
    @BuildingNo varchar(10),
    @StreetNo varchar(10),
    @Locality varchar(50),
    @Pincode numeric(6),
    @Contact numeric(10),
    @AddressId int
) 
AS 
BEGIN
BEGIN TRY
    if len(@Password)<8 
        return -1
    if @Contact<1000000000
        return -2
    if @Pincode<100000
        return -3
    Begin
        Begin tran
            Update Customer set  
                [Name]=@Name,
                [Password]=@Password,
                [Contact]=@Contact
            where [EmailId]=@EmailId 
            Update AddressTable set
                [BuildingNo]=@BuildingNo,
                [StreetNo]=@StreetNo,
                [Locality]=@Locality,
                [Pincode]=@Pincode
            where [AddressId]=@AddressId
        Commit
    end
    return 1
END TRY

BEGIN CATCH
    ROLLBACK
    RETURN -99
END CATCH 
END  
GO 

Declare @ReturnValue int
exec @ReturnValue=usp_UpdateCustomer
'Sivuuu','sivu@abc.com','Siva123456','6','21','Kilo',625078,9638527411,11
select @ReturnValue as ReturnValue
go

-----------------------Update the Status of Shipment(Optional -To be done)---------------



